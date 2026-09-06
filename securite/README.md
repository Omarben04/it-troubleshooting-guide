# Sécurité — Réaction face aux incidents

Cette section documente comment un technicien IT doit réagir face aux incidents de sécurité les plus fréquents — avec une logique commune à toutes les situations : **contenir d'abord, analyser ensuite, communiquer toujours**.

## Principe général avant toute réaction

Face à un incident de sécurité, l'ordre des priorités n'est jamais "réparer le plus vite possible" :

1. **Contenir** — empêcher que ça s'aggrave ou se propage (isoler la machine du réseau, par exemple)
2. **Préserver les preuves** — ne pas effacer ce qui pourrait servir à comprendre ce qui s'est passé (logs, fichiers suspects)
3. **Analyser** — comprendre l'étendue réelle, pas supposer
4. **Communiquer** — informer les bonnes personnes (responsable sécurité, direction, parfois autorités) au bon moment, pas trop tard, pas de panique injustifiée
5. **Corriger** — nettoyer, restaurer, une fois seulement l'analyse terminée
6. **Tirer les leçons** — documenter pour éviter la récidive

---

## Incident 1 — Email de phishing signalé par un utilisateur

### Diagnostic
Ne jamais cliquer sur les liens ou pièces jointes de l'email suspect, même "pour vérifier" — l'ouvrir dans un environnement isolé (sandbox) si une vraie analyse est nécessaire.

**Vérifier l'en-tête complet de l'email** (pas juste l'adresse affichée, souvent usurpée) :
- Dans Gmail : trois points → "Afficher l'original"
- Dans Outlook : Fichier → Propriétés → "En-têtes Internet"

Chercher les champs `Return-Path` et `Received` — ils révèlent souvent le vrai serveur d'envoi, différent de l'adresse affichée à l'utilisateur.

### Réaction immédiate
1. **Ne pas paniquer publiquement** — informer discrètement les équipes concernées, éviter de créer un mouvement de panique généralisé
2. **Bloquer l'expéditeur** au niveau de la passerelle email (pas juste dans la boîte de l'utilisateur — sinon d'autres employés restent exposés)
3. **Vérifier si d'autres employés ont reçu le même email** (recherche par objet/expéditeur dans la console d'administration de la messagerie)
4. **Si l'utilisateur a déjà cliqué** : passer immédiatement à l'incident 3 (poste potentiellement compromis)

### Ce qui aide à long terme
Sensibiliser régulièrement (pas une seule fois) via de vrais exemples anonymisés d'emails de phishing déjà reçus dans l'entreprise — plus efficace qu'une formation théorique générique.

---

## Incident 2 — Suspicion de ransomware (fichiers chiffrés, demande de rançon affichée)

### Diagnostic
Symptômes typiques : extensions de fichiers changées de façon inhabituelle, fichier "LISEZ-MOI" ou similaire apparu dans plusieurs dossiers, poste anormalement lent avant le chiffrement.

### Réaction immédiate — dans cet ordre précis

1. **Isoler immédiatement la machine du réseau** (débrancher le câble réseau, ou désactiver le Wi-Fi) — **avant** d'éteindre l'ordinateur. Un ransomware actif continue de se propager sur le réseau tant que la machine reste connectée.

2. **Ne pas éteindre la machine tout de suite** si une analyse forensique est envisagée (la mémoire vive peut contenir des informations utiles qui disparaissent à l'extinction) — sauf si le chiffrement est visiblement encore en cours, auquel cas l'arrêt immédiat limite les dégâts.

3. **Ne jamais payer la rançon soi-même ni encourager l'utilisateur à le faire** — cette décision, si elle se pose un jour, revient à la direction et n'est de toute façon jamais recommandée (aucune garantie de récupération, finance des groupes criminels).

4. **Identifier l'étendue** : quels dossiers réseau partagés étaient accessibles depuis ce poste ? Un ransomware se propage souvent via les lecteurs réseau montés.

5. **Vérifier les sauvegardes disponibles** avant toute tentative de restauration — s'assurer que la sauvegarde elle-même n'est pas déjà compromise (certains ransomwares modernes ciblent aussi les sauvegardes connectées).

### Ce qui aide à long terme
Des sauvegardes régulières, **déconnectées du réseau principal** entre deux sauvegardes (backup "air-gapped" ou au minimum non montées en permanence) — c'est la seule vraie protection fiable contre un ransomware, plus que n'importe quel antivirus.

---

## Incident 3 — Poste de travail potentiellement compromis (comportement anormal, activité suspecte)

### Signes qui doivent alerter
- Ventilateur qui tourne à plein régime sans raison apparente (minage de cryptomonnaie, processus caché)
- Programmes qui se lancent seuls
- Antivirus désactivé sans action de l'utilisateur
- Trafic réseau sortant anormal (connexions vers des adresses inconnues)

### Diagnostic
```bash
# Sur Windows, vérifier les connexions réseau actives
netstat -ano
```
Chercher des connexions établies vers des adresses IP inconnues, avec le PID du processus responsable.

tasklist | findstr <PID>

Identifier le nom du processus correspondant.

**Sur Linux**, l'équivalent avec osquery (comme utilisé dans ce projet) :
```bash
osqueryi --json "SELECT DISTINCT remote_address, pid FROM process_open_sockets WHERE remote_address != '';"
```

### Réaction immédiate
1. **Isoler du réseau** (même logique que pour un ransomware — limiter la propagation avant tout)
2. **Ne pas réinstaller immédiatement** sans avoir au moins noté/exporté les éléments suspects (processus, connexions, fichiers créés récemment) — sinon aucune leçon n'est tirée de l'incident
3. **Changer les mots de passe** de tous les comptes utilisés récemment depuis ce poste (email, VPN, applications internes) — un poste compromis a pu enregistrer les frappes clavier
4. **Vérifier les autres postes** du même réseau local pour des signes similaires

---

## Incident 4 — Tentative de connexion suspecte détectée par le SIEM (brute-force, scan)

### Diagnostic
Vérifier dans le SIEM (Graylog dans ce projet) le volume de tentatives, leur origine géographique probable, et si elles ciblent un seul compte ou plusieurs.

### Réaction proportionnée
- **Volume faible, IP isolée** : souvent un scan automatique de routine sur internet (très fréquent, pas nécessairement ciblé) — un bannissement automatique (fail2ban) suffit généralement
- **Volume élevé, ciblé sur un compte précis** : peut indiquer une tentative ciblée — vérifier si le compte visé a des privilèges élevés, envisager un changement de mot de passe préventif même sans certitude de compromission
- **Connexion réussie après plusieurs échecs** : traiter comme un incident de compromission de compte — voir Incident 5

---

## Incident 5 — Compte utilisateur potentiellement compromis

### Signes qui doivent alerter
Connexion depuis une localisation géographique inhabituelle, connexions à des heures atypiques, actions effectuées que l'utilisateur ne reconnaît pas.

### Réaction immédiate
1. **Désactiver le compte immédiatement** (pas juste changer le mot de passe — un attaquant avec une session déjà active peut continuer malgré un nouveau mot de passe)
2. **Forcer la déconnexion de toutes les sessions actives** (la plupart des services cloud/email proposent cette option)
3. **Vérifier l'historique récent** : emails envoyés, règles de transfert automatique ajoutées (technique fréquente pour continuer à espionner un compte même après reprise de contrôle), fichiers partagés
4. **Réactiver avec un nouveau mot de passe** uniquement après vérification, idéalement avec double authentification ajoutée si pas déjà en place

---

## Incident 6 — Clé USB ou périphérique externe inconnu trouvé (parking, réception, bureau)

### Le piège classique
Une clé USB abandonnée "par hasard" est une technique d'ingénierie sociale connue (déposée volontairement pour qu'un employé curieux la branche).

### Réaction
Ne **jamais** la brancher sur un poste de travail normal, même "juste pour voir à qui elle appartient". Si une analyse est nécessaire, utiliser une machine isolée dédiée à cet usage (non connectée au réseau de l'entreprise).

---

## Sommaire des principes de réaction, par urgence

| Situation | Isoler du réseau ? | Éteindre la machine ? | Contacter qui ? |
|---|---|---|---|
| Phishing signalé (pas cliqué) | Non | Non | Responsable sécurité (info) |
| Phishing cliqué | Oui | Non immédiatement | Responsable sécurité (urgent) |
| Ransomware suspecté | Oui, immédiatement | Selon le cas (voir détail) | Direction + responsable sécurité |
| Poste compromis (comportement anormal) | Oui | Non avant analyse | Responsable sécurité |
| Compte compromis | Non (le compte, pas la machine) | N/A | Responsable sécurité + utilisateur concerné |
| Périphérique USB inconnu | N/A | N/A | Signalement simple, pas d'alerte majeure |

---

## Incident 7 — Attaque par ingénierie sociale téléphonique (vishing)

### Le scénario classique
Un appel se présentant comme "le support informatique" ou "un fournisseur" demande un mot de passe, un accès distant, ou pousse à une action urgente ("votre compte va être bloqué dans 10 minutes").

### Signes qui doivent alerter
- Urgence artificielle créée pour empêcher de réfléchir
- Demande d'informations qu'un vrai support ne demande jamais (mot de passe complet, code de carte bancaire)
- Numéro d'appel masqué ou usurpant un numéro connu (spoofing d'appel, techniquement simple à réaliser)

### Réaction
Ne jamais donner d'information sensible pendant l'appel, quelle que soit l'urgence affichée. Raccrocher et rappeler soi-même le numéro officiel connu (pas celui donné par l'appelant) pour vérifier la légitimité de la demande.

---

## Incident 8 — Attaque par force brute ou dictionnaire sur un mot de passe

### Diagnostic
Vu dans ce projet avec fail2ban : de nombreuses tentatives de connexion successives, souvent automatisées, testant des mots de passe courants ou une liste connue de mots de passe déjà fuités (attaque par dictionnaire utilisant des bases de données de mots de passe compromis ailleurs).

### Réaction
- Verrouillage automatique après un nombre défini de tentatives (déjà en place avec fail2ban dans ce projet)
- Politique de mot de passe robuste imposée (longueur, complexité)
- Authentification à double facteur — rend une attaque par force brute inutile même si le mot de passe est deviné

---

## Incident 9 — Attaque de l'homme du milieu (Man-in-the-Middle / MITM)

### Le scénario
Un attaquant s'interpose entre deux communications (souvent sur un réseau Wi-Fi public non sécurisé) pour intercepter ou modifier les échanges, sans que les deux parties s'en rendent compte.

### Signes qui doivent alerter
Certificat SSL invalide ou changé de façon inattendue sur un site habituellement fiable, avertissement du navigateur sur un certificat "non fiable" pour un site normalement sécurisé.

### Réaction
Ne jamais ignorer un avertissement de certificat invalide, même si "ça marchait avant". Éviter les réseaux Wi-Fi publics non chiffrés pour des connexions sensibles (VPN d'entreprise recommandé systématiquement sur ce type de réseau).

---

## Incident 10 — Attaque par déni de service (DDoS)

### Diagnostic
Service ou serveur qui devient inaccessible ou anormalement lent, avec un volume de trafic entrant très supérieur à la normale.

**Sur ce projet, exemple de diagnostic** :
```bash
sudo ss -s
```
Statistiques résumées des connexions actives — un nombre anormalement élevé de connexions simultanées peut indiquer un DDoS en cours.

### Réaction
- Limitation de débit (rate limiting) au niveau du pare-feu ou du reverse proxy
- Dans un contexte cloud (comme Oracle Cloud utilisé ici), les fournisseurs proposent souvent une protection DDoS de base au niveau réseau, à vérifier/activer
- Documenter l'incident même s'il se résout de lui-même — utile pour identifier un pattern si ça se reproduit

---

## Incident 11 — Faille de sécurité critique annoncée publiquement (zero-day) sur un logiciel utilisé

### Le dilemme
Une faille zero-day (découverte et rendue publique avant qu'un correctif existe) impose d'agir vite, mais appliquer un correctif d'urgence sans test peut aussi casser un système en production.

### Réaction recommandée
1. **Évaluer l'exposition réelle** : le logiciel concerné est-il exposé sur internet, ou seulement en interne ? La criticité change complètement selon le cas
2. **Chercher une mesure de contournement temporaire** (désactiver une fonctionnalité précise, bloquer un port) en attendant un vrai correctif testé
3. **Ne jamais attendre passivement** un correctif officiel si l'exposition est critique — une mesure de contournement immédiate, même imparfaite, vaut mieux que rien

**Exemple concret dans ce projet** : quand un service Docker devient injoignable ou qu'une CVE est identifiée par un scan Trivy, la première réaction n'est pas de tout réinstaller, mais d'évaluer si la vulnérabilité est réellement exploitable dans le contexte précis (exposée publiquement ou non) avant de décider de l'urgence de la correction.

---

## Sécurité mobile — dimension spécifique (contexte Pradeo)

Cette section complète la partie MDM générale, avec un focus spécifique sur les menaces propres aux appareils mobiles.

### Incident 12 — Application mobile malveillante installée (sideloading ou store tiers)

### Le scénario
Un utilisateur installe une application en dehors du store officiel (APK téléchargé directement sur Android, souvent pour contourner une restriction ou obtenir une version gratuite d'une app payante), qui contient en réalité un malware.

### Signes qui doivent alerter
Batterie qui se vide anormalement vite, données mobiles consommées de façon inexpliquée, permissions demandées sans rapport avec la fonction annoncée de l'application (une application "lampe de poche" qui demande l'accès aux contacts et aux SMS, par exemple).

### Réaction
1. **Vérifier via le MDM** la liste des applications installées sur l'appareil, en particulier celles non présentes dans le catalogue d'applications autorisées
2. **Désinstaller à distance** si le MDM le permet, ou guider l'utilisateur pour une désinstallation manuelle immédiate
3. **Bloquer par politique** l'installation d'applications hors store officiel (restriction disponible dans la plupart des MDM, y compris Headwind MDM utilisé dans ce projet)

---

### Incident 13 — Réseau Wi-Fi public compromis ou faux point d'accès (rogue AP / Evil Twin)

### Le scénario
Un attaquant crée un faux point d'accès Wi-Fi avec un nom identique ou très proche d'un réseau légitime (ex : "Aeroport_WiFi_Free" au lieu de "Aeroport_WiFi"), pour intercepter le trafic des appareils qui s'y connectent.

### Réaction
Politique MDM interdisant la connexion automatique à des réseaux Wi-Fi non répertoriés, VPN d'entreprise activé automatiquement dès qu'une connexion Wi-Fi publique est détectée (fonctionnalité disponible sur certains MDM avancés).

---

### Incident 14 — SMS de phishing (smishing) reçu sur un appareil professionnel

### Le scénario
Un SMS usurpant une identité connue (banque, livreur, support IT) pousse à cliquer un lien malveillant, technique identique au phishing par email mais via SMS, souvent moins bien filtrée.

### Réaction
Même logique que le phishing email : ne jamais cliquer, signaler à l'équipe sécurité, vérifier si d'autres employés ont reçu un SMS similaire (signe d'une campagne ciblée sur l'entreprise plutôt qu'un envoi générique aléatoire).

---

### Incident 15 — Appareil mobile utilisé pour une attaque interne (employé malveillant)

### Le scénario, plus sensible
Un appareil professionnel utilisé volontairement par son détenteur pour exfiltrer des données de l'entreprise (photos d'écran de documents confidentiels, transfert de fichiers vers un compte personnel).

### Réaction
Ce cas dépasse largement le rôle technique d'un technicien IT — la détection technique (via les logs MDM, les politiques de restriction de partage) doit être immédiatement transmise à la direction et aux RH, jamais traitée ou investiguée seul comme un simple incident technique classique.

---

## Sommaire complet des principes de réaction, par urgence

| Situation | Isoler du réseau ? | Éteindre la machine ? | Contacter qui ? |
|---|---|---|---|
| Phishing signalé (pas cliqué) | Non | Non | Responsable sécurité (info) |
| Phishing cliqué | Oui | Non immédiatement | Responsable sécurité (urgent) |
| Ransomware suspecté | Oui, immédiatement | Selon le cas (voir détail) | Direction + responsable sécurité |
| Poste compromis (comportement anormal) | Oui | Non avant analyse | Responsable sécurité |
| Compte compromis | Non (le compte, pas la machine) | N/A | Responsable sécurité + utilisateur concerné |
| Périphérique USB inconnu | N/A | N/A | Signalement simple |
| Vishing (appel frauduleux) | N/A | N/A | Signalement, pas d'action technique immédiate |
| DDoS en cours | N/A (protection réseau) | Non | Équipe infrastructure + fournisseur cloud |
| Zero-day critique exposé | Dépend de l'exposition | Non | Responsable sécurité (urgent si exposé) |
| App mobile malveillante détectée | Retrait à distance via MDM | N/A | Utilisateur + responsable sécurité si sensible |
| Smishing reçu | N/A | N/A | Signalement, vérifier si campagne ciblée |
| Suspicion d'employé malveillant | Ne pas investiguer seul | N/A | Direction + RH immédiatement |
