# Windows — Pannes courantes

## Panne 1 — Lenteur générale du poste

**Diagnostic** :

Ctrl+Shift+Echap → Gestionnaire des tâches → onglet Performance

Vérifier dans l'ordre : CPU (processus qui consomme anormalement), Mémoire (RAM saturée), Disque (100% d'utilisation = souvent un disque mécanique en fin de vie, ou Windows Update en tâche de fond).

**Commande utile** :

resmon


---

## Panne 2 — Pas d'accès réseau / Internet

**Diagnostic étape par étape** :

ipconfig /all

Vérifier si une adresse IP est bien attribuée (pas de `169.254.x.x`, signe d'un échec DHCP).

ping 127.0.0.1
ping 8.8.8.8
ping google.com

Si le dernier échoue mais pas le précédent : problème DNS, pas de connexion.

**Solution courante** :

ipconfig /flushdns
ipconfig /release
ipconfig /renew


---

## Panne 3 — Un service/application ne démarre plus

**Diagnostic** :

eventvwr.msc → Journaux Windows → Application
services.msc


---

## Panne 4 — Écran bleu (BSOD)

**Diagnostic** :
Noter le code d'arrêt affiché (ex : `MEMORY_MANAGEMENT`, `IRQL_NOT_LESS_OR_EQUAL`).

Panneau de configuration → Outils d'administration → Observateur d'événements → Journaux Windows → Système

Chercher l'erreur juste avant le redémarrage.

**Causes fréquentes** : pilote récemment mis à jour, RAM défectueuse, disque en fin de vie.

**Test mémoire** :

mdsched.exe

(Diagnostic de la mémoire Windows, redémarre et teste la RAM)

---

## Panne 5 — Windows Update bloqué ou en échec

**Diagnostic** :

Paramètres → Windows Update → Historique des mises à jour

Vérifier le code d'erreur exact.

**Solution courante — réinitialiser les composants de mise à jour** :

net stop wuauserv
net stop cryptSvc
net stop bits
net stop msiserver
ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
ren C:\Windows\System32\catroot2 catroot2.old
net start wuauserv
net start cryptSvc
net start bits
net start msiserver


---

## Panne 6 — Imprimante réseau introuvable ou bloquée

**Diagnostic** :

ping <adresse-ip-imprimante>

Vérifier que l'imprimante répond sur le réseau.

**File d'attente bloquée** :

services.msc → Spouleur d'impression → Redémarrer

Ou en ligne de commande :

net stop spooler
del /Q /F %systemroot%\System32\spool\PRINTERS*.*
net start spooler


---

## Panne 7 — Espace disque insuffisant

**Diagnostic** :

Explorateur de fichiers → Clic droit sur C: → Propriétés

Ou pour identifier ce qui prend de la place :

Paramètres → Système → Stockage


**Nettoyage rapide** :

cleanmgr

(Nettoyage de disque, inclut souvent les fichiers Windows Update obsolètes, très volumineux)

---

## Panne 8 — Session utilisateur corrompue / profil qui ne charge pas

**Symptôme** : l'utilisateur se connecte mais obtient un "profil temporaire", ou l'ouverture de session reste bloquée sur un écran noir.

**Diagnostic** :

eventvwr.msc → Journaux Windows → Application

Chercher les erreurs liées à "User Profile Service".

**Solution courante** : renommer le dossier profil corrompu (`C:\Users\NomUtilisateur.bak`) et forcer la recréation d'un profil propre à la prochaine connexion (après sauvegarde des données).

---

## Panne 9 — Ordinateur ne démarre plus (avant Windows)

**Diagnostic** :
- Rien à l'écran, pas de bip : problème matériel (alimentation, carte mère)
- Bips répétés au démarrage : erreur POST, souvent RAM mal insérée
- Logo Windows qui boucle : problème de démarrage logiciel

**Outils de réparation** :

Environnement de récupération Windows (WinRE) → Dépannage → Options avancées → Réparation du démarrage


---

## Panne 10 — Assistance à distance sans logiciel préinstallé chez le client

### Contexte
Un client a besoin d'aide, mais aucun logiciel de prise en main à distance n'est déjà installé, et il n'est pas à l'aise pour en installer un seul.

### Solutions, du plus simple au plus technique

**1. Assistance rapide Windows intégrée (aucune installation)**
Outil natif présent sur Windows 10 et 11 : le client tape "Assistance rapide" dans la recherche, clique "Obtenir de l'aide", génère un code que le technicien saisit de son côté. Aucune installation nécessaire.

**2. Solutions portables sans installation**
AnyDesk ou TeamViewer QuickSupport proposent une version portable (simple exécutable, pas de droits admin nécessaires).

**3. Si le client ne peut vraiment rien télécharger**
Guider par téléphone via le partage d'écran d'une messagerie déjà installée (WhatsApp, Teams, Zoom).

### Ce que cette situation apprend
Anticiper ce type de blocage avant qu'il n'arrive — garder un outil ou un lien toujours prêt, plutôt que de chercher une solution en plein incident.

---

## Panne 11 — VPN qui ne se connecte pas ou coupe fréquemment

**Diagnostic** :

ping <adresse-serveur-vpn>

Vérifier la connectivité brute vers le serveur avant de blâmer le VPN lui-même.

eventvwr.msc → Journaux Windows → Application → rechercher les erreurs du client VPN


**Cas fréquent** : conflit d'adresses IP entre le réseau local et le réseau distant (les deux utilisent la même plage, ex : 192.168.1.x des deux côtés) — le VPN se connecte mais aucun trafic ne passe correctement.

**Solution courante** : redémarrer l'adaptateur réseau virtuel du VPN :

ncpa.cpl

Clic droit sur l'adaptateur VPN → Désactiver → Réactiver.

---

## Panne 12 — Compte utilisateur verrouillé (Active Directory)

**Diagnostic (depuis un poste d'administration)** :

Utilisateurs et ordinateurs Active Directory → rechercher l'utilisateur → onglet Compte

Vérifier si la case "Le compte est verrouillé" est cochée, et depuis quand.

**Identifier la source du verrouillage** (utile si le verrouillage se reproduit) :

eventvwr.msc → Journaux Windows → Sécurité → filtrer sur l'ID d'événement 4740

Indique depuis quelle machine la tentative de connexion a échoué plusieurs fois (souvent une session oubliée ouverte quelque part avec un ancien mot de passe).

---

## Panne 13 — Pilote de périphérique manquant ou défaillant

**Diagnostic** :

devmgmt.msc

Gestionnaire de périphériques — chercher les icônes avec un point d'exclamation jaune.

**Solution courante** :
Clic droit sur le périphérique → Mettre à jour le pilote → Rechercher automatiquement.

Si ça échoue, désinstaller puis forcer une nouvelle détection :
Clic droit → Désinstaller l'appareil → puis dans le menu "Action" → "Rechercher les modifications sur le matériel".

---

## Panne 14 — Problème de certificat / site web qui ne s'affiche pas correctement dans le navigateur

**Diagnostic** :
Vérifier la date et l'heure du système — un décalage d'horloge important fait souvent échouer la validation de certificats SSL.

Paramètres → Heure et langue → vérifier que "Régler l'heure automatiquement" est actif


**Solution courante navigateur** :
Vider le cache et les données de navigation, notamment les certificats mis en cache.

---

## Panne 15 — Impression réseau, diagnostic détaillé

### Étape 1 — Vérifier la connectivité réseau vers l'imprimante

ping <adresse-ip-imprimante>

Si aucune réponse : problème réseau (câble, Wi-Fi, ou imprimante éteinte) avant même de penser au pilote.

### Étape 2 — Vérifier que le service d'impression fonctionne

services.msc → Spouleur d'impression → doit être "En cours d'exécution"


### Étape 3 — Vérifier la file d'attente bloquée
Une impression bloquée en tête de file empêche souvent tous les documents suivants de s'imprimer, même sans rapport avec le problème initial.

**Vider complètement la file (technique) :**

net stop spooler
del /Q /F %systemroot%\System32\spool\PRINTERS*.*
net start spooler


### Étape 4 — Vérifier le pilote correspond bien au modèle exact
Un pilote générique ou pour un modèle proche mais différent peut sembler fonctionner partiellement (page de test OK) mais échouer sur de vrais documents complexes.

### Étape 5 — Cas spécifique : imprimante visible mais "hors ligne"

Panneau de configuration → Périphériques et imprimantes → clic droit sur l'imprimante → "Utiliser l'imprimante en ligne"

Windows marque parfois une imprimante "hors ligne" après une brève coupure réseau, sans se remettre à jour automatiquement.

### Étape 6 — Scanner réseau qui ne fonctionne pas alors que l'impression fonctionne
Diagnostic séparé nécessaire : le scan utilise souvent un protocole différent (SMB pour l'envoi vers un dossier partagé, ou une app dédiée).

**Vérifier le partage réseau de destination** :

net share

Confirme que le dossier partagé cible existe bien et est accessible en écriture pour le compte utilisé par le scanner.

**Cas fréquent** : le mot de passe du compte utilisé par le scanner pour accéder au dossier partagé a expiré ou changé, sans que personne n'ait mis à jour la configuration du scanner — l'impression continue de fonctionner (autre protocole) pendant que le scan échoue silencieusement.
