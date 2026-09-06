# macOS — Pannes courantes

## Panne 1 — Application qui ne répond plus

**Diagnostic** :

Cmd+Option+Echap

Force Quit — équivalent du gestionnaire de tâches Windows.

**Terminal** :
```bash
top -o cpu
```

---

## Panne 2 — Problème réseau Wi-Fi

**Diagnostic** :
```bash
ping 8.8.8.8
```

Réinitialiser le cache réseau :
```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

**Réinitialiser complètement les paramètres réseau** (si le problème persiste) :

Préférences Système → Réseau → Wi-Fi → Avancé → Supprimer le réseau puis le reconnecter


---

## Panne 3 — Disque plein / lenteur

**Diagnostic** :

Pomme → À propos de ce Mac → Stockage

Ou en ligne de commande :
```bash
df -h
```

**Identifier les gros fichiers** :
```bash
du -sh /Users/*/* 2>/dev/null | sort -rh | head -10
```

---

## Panne 4 — Mac qui ne démarre plus normalement

**Mode sans échec** (pour isoler un problème logiciel/extension) :

Redémarrer en maintenant Maj enfoncée


**Mode de récupération** (réparer le disque, réinstaller macOS) :

Redémarrer en maintenant Cmd+R


**Vérifier et réparer le disque depuis le mode de récupération** :

Utilitaire de disque → Sélectionner le disque → Premiers secours


---

## Panne 5 — Un service/processus consomme trop de ressources

**Diagnostic** :

Cmd+Espace → taper "Moniteur d'activité"

Onglet CPU, trier par "% CPU" pour identifier le processus fautif.

**En ligne de commande, tuer un processus bloqué** :
```bash
kill <PID>
kill -9 <PID>
```

---

## Panne 6 — Impossible de se connecter en SSH à un Mac distant

**Vérifier que le partage à distance est activé** :

Préférences Système → Partage → Connexion à distance (cocher)


**Diagnostic côté client** :
```bash
ssh -v utilisateur@ip-du-mac
```

---

## Panne 7 — Mises à jour macOS qui échouent

**Diagnostic** :

Préférences Système → Général → Mise à jour de logiciels


**Solution courante** : redémarrer en mode sans échec pour forcer une vérification/réparation du disque système avant de retenter la mise à jour.

**Vider le cache de mise à jour (en ligne de commande, pour les administrateurs avancés)** :
```bash
sudo rm -rf /Library/Updates/*
```

---

## Panne 8 — Permissions refusées sur un fichier/dossier

**Diagnostic** :
```bash
ls -la <chemin>
```

**Réparer les permissions d'un dossier** :
```bash
sudo chown -R $(whoami) <chemin>
sudo chmod -R 755 <chemin>
```

---

## Panne 9 — VPN qui ne se connecte pas

**Diagnostic** :
```bash
ping <adresse-serveur-vpn>
```

**Vérifier les logs système** :

Console.app → rechercher "vpn" dans les logs récents


---

## Panne 10 — Mot de passe oublié / compte verrouillé

**Réinitialiser depuis le mode de récupération** :

Redémarrer en maintenant Cmd+R → Utilitaires → Terminal

Dans le Terminal du mode récupération :
```bash
resetpassword
```

---

## Panne 11 — Périphérique externe non reconnu (clé USB, disque externe)

**Diagnostic** :
```bash
system_profiler SPUSBDataType
```
Liste tous les périphériques USB détectés par le système, même si non montés.

Utilitaire de disque → vérifier si le périphérique apparaît en grisé (non monté)


---

## Panne 12 — Certificat invalide / erreur de date système

**Diagnostic** :

Préférences Système → Général → Date et heure → vérifier que le réglage automatique est actif


---

## Panne 13 — Sauvegarde Time Machine qui échoue

**Diagnostic** :

Préférences Système → Time Machine → vérifier le message d'erreur exact et la date de la dernière sauvegarde réussie


**Cas fréquent** : disque de sauvegarde plein ou déconnecté au moment prévu de la sauvegarde automatique.
