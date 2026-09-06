# Linux — Pannes courantes

> Note sur les distributions : les commandes de base (top, ps, ip, systemctl) sont identiques partout. Les différences apparaissent surtout pour la gestion des paquets, du pare-feu, et des logs — précisées à chaque fois que c'est pertinent.
> - **Debian / Ubuntu** : gestion de paquets `apt`, pare-feu souvent `ufw`
> - **RedHat / CentOS / Oracle Linux / Fedora** : gestion de paquets `dnf` (ou `yum` sur les anciennes versions), pare-feu souvent `firewalld`, SELinux généralement actif par défaut

---

## Panne 1 — Serveur qui ne répond plus / lenteur

**Diagnostic (toutes distributions)** :
```bash
top
htop
df -h
free -h
```
`free -h` : vérifier la colonne "available" (RAM réellement disponible), pas juste "free".
`df -h` : vérifier qu'aucune partition n'est à 100% (un disque plein bloque souvent silencieusement des services entiers, notamment les bases de données).

---

## Panne 2 — Un service ne démarre pas

**Diagnostic (toutes distributions, systemd)** :
```bash
sudo systemctl status <nom-du-service>
sudo journalctl -u <nom-du-service> --no-pager | tail -50
```

---

## Panne 3 — Problème réseau

**Diagnostic étape par étape (toutes distributions)** :
```bash
ip a
ping -c 4 8.8.8.8
sudo ss -tlnp
```
`ss -tlnp` vérifie quels ports sont réellement en écoute — utile pour diagnostiquer "le service tourne mais n'est pas accessible".

**Exemple concret rencontré en pratique** : un service Docker semblait fonctionner (`docker ps` montrait "Up"), mais restait injoignable depuis l'extérieur. `ss -tlnp` a révélé que le port écouté en interne (8080) ne correspondait pas au port exposé dans la configuration (8443).

---

## Panne 4 — Pare-feu qui bloque un service

**Sur Debian / Ubuntu (ufw)** :
```bash
sudo ufw status
sudo ufw allow <port>/tcp
sudo ufw reload
```

**Sur RedHat / CentOS / Oracle Linux (firewalld)** :
```bash
sudo firewall-cmd --list-ports
sudo firewall-cmd --permanent --add-port=<port>/tcp
sudo firewall-cmd --reload
```

---

## Panne 5 — Système de fichiers en lecture seule / disque plein

**Diagnostic (toutes distributions)** :
```bash
df -h
du -sh /* 2>/dev/null | sort -rh | head -10
```

**Cas fréquent** : logs qui grossissent indéfiniment (`/var/log`), sans rotation configurée.
```bash
cat /etc/logrotate.conf
```

---

## Panne 6 — Permissions refusées (Permission denied)

**Diagnostic (toutes distributions)** :
```bash
ls -la <fichier ou dossier>
whoami
groups
```

**Sur RedHat / CentOS / Oracle Linux — SELinux souvent actif par défaut**, une erreur 403 ou "Permission denied" peut survenir même avec des permissions Unix correctes :
```bash
sudo ausearch -m avc --start recent
sudo semanage fcontext -a -t <contexte> '<chemin>(/.*)?'
sudo restorecon -Rv <chemin>
```

**Sur Debian / Ubuntu**, SELinux est rarement actif par défaut (AppArmor peut jouer un rôle similaire) :
```bash
sudo aa-status
```

---

## Panne 7 — Impossible de se connecter en SSH

**Diagnostic (toutes distributions)** :
```bash
ssh -v utilisateur@serveur
```

**Vérifier côté serveur** :
```bash
sudo systemctl status sshd
```

Logs d'authentification :
- **RedHat / CentOS / Oracle Linux** : `sudo tail -50 /var/log/secure`
- **Debian / Ubuntu** : `sudo tail -50 /var/log/auth.log`

---

## Panne 8 — Trop de tentatives de connexion suspectes (sécurité)

**Diagnostic** :
- **RedHat/CentOS/Oracle Linux** : `sudo grep "Failed password" /var/log/secure | tail -20`
- **Debian/Ubuntu** : `sudo grep "Failed password" /var/log/auth.log | tail -20`

**Mise en place d'une protection automatique (fail2ban, toutes distributions)** :
```bash
sudo fail2ban-client status sshd
```

**Installation** :
- **Debian/Ubuntu** : `sudo apt install fail2ban`
- **RedHat/CentOS/Oracle Linux** : nécessite le dépôt EPEL au préalable :
```bash
sudo dnf install epel-release
sudo dnf install fail2ban
```

---

## Panne 9 — Processus zombie ou bloqué

**Diagnostic (toutes distributions)** :
```bash
ps aux | grep <nom-processus>
kill <PID>
kill -9 <PID>
```

---

## Panne 10 — Un conteneur Docker ne démarre pas ou redémarre en boucle

**Diagnostic (toutes distributions)** :
```bash
docker ps -a
docker logs <nom-conteneur> --tail 50
```

**Cas fréquent** : conflit de port déjà utilisé par un autre service.
```bash
sudo ss -tlnp | grep <port>
```

---

## Panne 11 — Installation de paquet qui échoue

**Sur Debian / Ubuntu** :
```bash
sudo apt update
sudo apt install <paquet>
```
Si erreur de dépendances :
```bash
sudo apt --fix-broken install
```

**Sur RedHat / CentOS / Oracle Linux** :
```bash
sudo dnf install <paquet>
```
Si le paquet n'est pas trouvé, vérifier si le dépôt EPEL est nécessaire et activé :
```bash
sudo dnf repolist
```

---

## Panne 12 — Mise à jour système qui échoue ou bloque

**Sur Debian / Ubuntu** :
```bash
sudo apt update && sudo apt upgrade
```
Si un processus bloque (`dpkg was interrupted`) :
```bash
sudo dpkg --configure -a
```

**Sur RedHat / CentOS / Oracle Linux** :
```bash
sudo dnf update
```
Si le cache de métadonnées semble corrompu :
```bash
sudo dnf clean all
sudo dnf makecache
```

---

## Panne 13 — VPN qui ne se connecte pas

**Diagnostic (toutes distributions)** :
```bash
ping <adresse-serveur-vpn>
sudo journalctl -u NetworkManager --no-pager | tail -30
```

**Vérifier la configuration** :
```bash
ip route
```
Vérifier qu'une route par défaut passe bien par l'interface VPN une fois connecté.

---

## Panne 14 — Compte utilisateur verrouillé ou mot de passe oublié

**Réinitialiser le mot de passe d'un utilisateur local (accès root/sudo requis)** :
```bash
sudo passwd <nom-utilisateur>
```

**Si le compte root lui-même est inaccessible** : redémarrer en mode single-user (GRUB), ajouter `single` ou `init=/bin/bash` à la ligne de démarrage, puis :
```bash
mount -o remount,rw /
passwd root
```

---

## Panne 15 — Pilote ou périphérique non reconnu

**Diagnostic** :
```bash
lsusb
lspci
dmesg | tail -30
```
`dmesg` affiche les messages du noyau, souvent révélateurs d'un périphérique mal détecté ou d'un pilote manquant.

---

## Panne 16 — Certificat SSL invalide / erreur de date

**Diagnostic** :
```bash
date
```
Vérifier que la date système est correcte — un décalage important fait échouer la validation de certificats.

**Synchroniser l'heure** :
```bash
sudo systemctl status chronyd
sudo chronyc sources
```
(ou `ntpd` selon la distribution)

---

## Panne 17 — Sauvegarde ou restauration de données

**Sauvegarde simple d'un dossier avec archivage** :
```bash
tar -czvf sauvegarde.tar.gz /chemin/dossier
```

**Restauration** :
```bash
tar -xzvf sauvegarde.tar.gz -C /chemin/destination
```

**Synchronisation incrémentale (plus efficace pour de gros volumes récurrents)** :
```bash
rsync -avh --progress /source/ /destination/
```
