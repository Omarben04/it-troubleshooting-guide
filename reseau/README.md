# 🌐 Réseau — DNS, DHCP, VLAN en profondeur

Cette section va plus loin que le simple "ping ça marche ou pas" abordé dans les fichiers par OS. Ici, on entre dans la logique des services réseau eux-mêmes.

---

## DNS — comprendre avant de dépanner

### Le principe en une phrase
Le DNS traduit un nom (google.com) en adresse IP (142.250.x.x). Sans lui, internet fonctionnerait encore techniquement, mais personne ne retiendrait les adresses IP de chaque site.

### Diagnostic étape par étape, sur n'importe quel OS

**Windows** :

nslookup google.com


**Linux/Mac** :
```bash
dig google.com
```
ou plus simple :
```bash
nslookup google.com
```

Si ça échoue, isoler où ça bloque exactement :
```bash
dig @8.8.8.8 google.com
```
Cette commande interroge directement un serveur DNS public (Google), en contournant celui configuré par défaut. Si ça fonctionne avec `@8.8.8.8` mais pas sans, le problème vient du serveur DNS habituellement utilisé, pas de la connexion internet elle-même.

### Panne fréquente — cache DNS corrompu

**Windows** :

ipconfig /flushdns


**Linux** (selon le service utilisé) :
```bash
sudo systemctl restart systemd-resolved
```

**macOS** :
```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

### Panne fréquente — propagation DNS après changement

Quand on modifie un enregistrement DNS (par exemple, faire pointer un nom de domaine vers une nouvelle IP), le changement ne se voit pas instantanément partout. C'est lié à la notion de **TTL** (Time To Live) — la durée pendant laquelle les serveurs DNS intermédiaires gardent l'ancienne réponse en cache avant de revérifier.

Vérifier le TTL configuré pour un enregistrement :
```bash
dig google.com
```
Chercher la ligne avec la valeur numérique juste avant "IN A" — c'est le TTL en secondes.

---

## DHCP — comprendre avant de dépanner

### Le principe en une phrase
Le DHCP attribue automatiquement une adresse IP à chaque appareil qui se connecte au réseau, sans configuration manuelle.

### Panne fréquente — appareil avec une adresse en 169.254.x.x

Cette plage d'adresses (APIPA, Automatic Private IP Addressing) signifie que l'appareil n'a **pas réussi** à obtenir d'adresse d'un serveur DHCP, et s'est attribué une adresse de secours qui ne permet aucune vraie communication réseau.

**Causes possibles, dans l'ordre de fréquence** :
1. Câble réseau débranché ou défectueux (le plus fréquent, à vérifier en premier)
2. Serveur DHCP en panne ou injoignable
3. Plage d'adresses DHCP épuisée (tous les baux déjà attribués, aucune adresse libre)

**Diagnostic sur le serveur DHCP lui-même (Linux, exemple avec isc-dhcp-server)** :
```bash
sudo systemctl status isc-dhcp-server
sudo journalctl -u isc-dhcp-server --no-pager | tail -30
```

### Panne fréquente — conflit d'adresse IP

Deux appareils avec la même adresse IP sur le même réseau, généralement parce qu'une adresse a été configurée manuellement dans la même plage que celle gérée par le DHCP.

**Diagnostic Windows** :

arp -a

Affiche la table de correspondance IP/adresse physique — une IP en double avec deux adresses physiques différentes confirme le conflit.

---

## VLAN — comprendre avant de dépanner

### Le principe en une phrase
Un VLAN (réseau local virtuel) permet de séparer logiquement plusieurs réseaux sur les mêmes équipements physiques, comme si chaque VLAN était son propre réseau isolé, même si tout passe par le même câblage et les mêmes switchs.

### Pourquoi c'est utile, concrètement
Par exemple, séparer le réseau des employés du réseau des visiteurs, ou isoler les caméras de surveillance du reste du réseau — même infrastructure physique, mais aucune communication directe possible entre les deux VLAN sans passer par un routage explicite.

### Panne fréquente — appareil qui ne communique pas alors que le câble et le port semblent corrects

**Diagnostic sur un switch manageable** :
Vérifier que le port utilisé par l'appareil est bien configuré sur le bon VLAN (souvent appelé "VLAN natif" ou "access VLAN" selon le port).

Un appareil branché sur un port configuré pour un VLAN différent de celui attendu communiquera avec le mauvais groupe de machines, ou avec aucune si ce VLAN est isolé — sans qu'aucune erreur explicite n'apparaisse à l'utilisateur, qui verra juste "pas d'accès réseau".

### Panne fréquente — VLAN qui ne passe pas entre deux switchs

Nécessite que le lien entre les deux switchs soit configuré en mode "trunk" (qui transporte plusieurs VLAN à la fois), pas en simple "access" (qui ne transporte qu'un seul VLAN). Un oubli fréquent lors de l'ajout d'un nouveau switch dans une infrastructure existante.

---

## Un exemple qui relie les trois notions

Un nouvel employé arrive, branche son ordinateur, et n'a aucun accès réseau.

**Démarche de diagnostic complète** :
1. Vérifier d'abord la couche la plus basse : le câble et le port physique fonctionnent-ils (voyants allumés) ?
2. Vérifier le VLAN configuré sur ce port — correspond-il bien au réseau "employés" et non au réseau "visiteurs" par erreur ?
3. Une fois le VLAN confirmé correct, vérifier si une adresse IP a bien été attribuée par le DHCP (`ipconfig` ou `ip a`) — une adresse en 169.254.x.x indique un échec DHCP malgré un VLAN correct
4. Une fois une vraie adresse IP confirmée, vérifier enfin la résolution DNS (`nslookup` ou `dig`) — un accès réseau local qui fonctionne mais aucun accès à internet pointe souvent vers un problème DNS, pas un problème de connectivité de base

Cette démarche en couches, de la plus basse à la plus haute, évite de sauter directement à une hypothèse (souvent DNS, le réflexe le plus courant) alors que le vrai problème se situe plus bas dans la chaîne.

---

## Sous-réseaux et masques — comprendre avant de dépanner

### Le principe en une phrase
Un masque de sous-réseau détermine quelle partie d'une adresse IP identifie le réseau, et quelle partie identifie l'appareil précis à l'intérieur de ce réseau.

### Pourquoi ça cause des pannes
Deux appareils avec des adresses IP qui semblent proches (192.168.1.10 et 192.168.2.10) peuvent en réalité être sur deux réseaux complètement différents si le masque limite chaque réseau à une petite plage — ils ne pourront jamais se voir directement sans passer par un routeur, même branchés sur le même switch physique.

### Diagnostic rapide
**Windows** :

ipconfig

Regarder la ligne "Masque de sous-réseau" à côté de l'adresse IP.

**Linux** :
```bash
ip a
```
Le masque apparaît directement après l'adresse IP, sous la forme `/24` (équivalent à 255.255.255.0).

**Calculer rapidement si deux adresses sont sur le même réseau** : avec un masque `/24`, seuls les 3 premiers groupes de chiffres comptent — 192.168.1.10 et 192.168.1.50 sont sur le même réseau, mais 192.168.1.10 et 192.168.2.10 ne le sont pas.

---

## NAT (traduction d'adresse réseau) — comprendre avant de dépanner

### Le principe en une phrase
Le NAT permet à plusieurs appareils avec des adresses IP privées (192.168.x.x) de partager une seule adresse IP publique pour sortir sur internet — c'est ce que fait ta box internet à la maison, ou une passerelle d'entreprise.

### Panne fréquente — un service interne inaccessible depuis l'extérieur

Un serveur avec une adresse IP privée ne peut pas être atteint directement depuis internet, même si le service tourne parfaitement en interne. Il faut une règle de redirection de port (port forwarding) explicite sur l'équipement qui fait le NAT, pour rediriger un port externe vers l'adresse et le port internes du serveur.

**Exemple concret dans ce projet** : sur Oracle Cloud, chaque VM a directement une IP publique assignée (pas de NAT à gérer manuellement), mais dans une infrastructure d'entreprise classique avec une seule IP publique pour tout un site, cette étape de redirection de port est indispensable et souvent oubliée lors de l'ajout d'un nouveau service.

---

## Pare-feu et ports — diagnostic en profondeur

### Le principe à bien comprendre
Un service peut parfaitement fonctionner en local sur une machine, être bloqué uniquement par le pare-feu du système lui-même, et être bloqué une seconde fois par un pare-feu réseau plus large (routeur, cloud) — trois niveaux distincts, chacun capable de bloquer indépendamment des deux autres.

### Méthode de diagnostic en couches, du plus proche au plus loin

**1. Le service écoute-t-il vraiment sur la machine ?**
```bash
sudo ss -tlnp | grep <port>
```
Si rien n'apparaît : le problème n'est pas réseau, le service lui-même ne démarre pas correctement sur ce port.

**2. Le pare-feu local du système bloque-t-il ce port ?**

Linux (firewalld) :
```bash
sudo firewall-cmd --list-ports
```

Linux (ufw) :
```bash
sudo ufw status
```

Windows :

Panneau de configuration → Pare-feu Windows Defender → Paramètres avancés


**3. Le pare-feu réseau plus large bloque-t-il ce port ?**
Dans ce projet précis, deux niveaux distincts existaient : le pare-feu système de la VM (firewalld) ET les règles réseau Oracle Cloud (Security Lists) — un port ouvert d'un seul côté mais pas de l'autre produit exactement le même symptôme (connexion refusée), ce qui peut faire perdre du temps si on ne vérifie qu'un seul des deux niveaux.

**Test complet, de l'intérieur vers l'extérieur** :
```bash
# Depuis la machine elle-même
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:<port>

# Depuis une autre machine du même réseau local
curl -s -o /dev/null -w "%{http_code}\n" http://<ip-privee>:<port>

# Depuis internet
curl -s -o /dev/null -w "%{http_code}\n" http://<ip-publique>:<port>
```
Si le premier test réussit mais pas le second : pare-feu système local à vérifier. Si le second réussit mais pas le troisième : pare-feu réseau/cloud à vérifier.

---

## Cas de dépannage complexe — un bureau entier perd l'accès à un serveur interne, mais internet fonctionne

### Le scénario
Un utilisateur signale ne plus accéder à un serveur de fichiers interne, mais son accès internet fonctionne normalement — ce qui semble contre-intuitif si on pense "problème réseau" au sens large.

### Démarche de diagnostic

1. **Vérifier si le problème touche un seul utilisateur ou tout le bureau** — si toute une zone est touchée mais pas internet, ça oriente immédiatement vers un problème de routage interne, pas vers le fournisseur d'accès internet
2. **Vérifier la résolution du nom du serveur interne** :
```bash
nslookup serveur-fichiers.entreprise.local
```
Un serveur DNS interne dédié (différent du DNS public utilisé pour internet) peut être en panne sans affecter la navigation internet classique.

3. **Si la résolution DNS fonctionne, tester directement par IP** :
```bash
ping <ip-du-serveur>
```
Si ça répond par IP mais pas par nom : confirmé, le problème est DNS interne, pas un problème réseau général.

4. **Si même l'IP ne répond pas** : vérifier le routage entre le VLAN de l'utilisateur et celui du serveur — un changement de configuration réseau récent (nouvelle règle de routage, VLAN mal configuré) est la cause la plus fréquente quand plusieurs utilisateurs sont touchés simultanément, contrairement à une panne isolée sur un seul poste.

### Ce que ce cas illustre
Un accès internet qui fonctionne ne garantit absolument rien sur l'état du réseau interne — ce sont souvent deux chemins réseau complètement distincts, avec des points de défaillance différents.
