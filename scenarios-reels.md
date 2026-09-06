# Situations réelles — dilemmes rencontrés par un technicien IT

Contrairement aux fiches techniques du reste de ce guide, cette section raconte des situations concrètes telles qu'elles se présentent vraiment : avec un dilemme, pas juste une solution évidente, et plusieurs options à peser selon le contexte.

---

## Situation 1 — Un client a un problème, mais aucun accès à distance n'est en place

### Le contexte
Un technicien reçoit une demande d'un client : son ordinateur a un problème qu'il faut corriger à distance. Mais aucun outil de prise en main à distance n'est installé sur le poste du client, et celui-ci ne sait pas forcément comment en installer un seul.

### Le vrai dilemme
Le technicien ne peut pas se déplacer immédiatement, le client attend une réponse, et chaque minute sans solution ressemble à un blocage complet — alors qu'il existe en fait plusieurs chemins possibles, à condition de les connaître à l'avance plutôt que de les chercher dans l'urgence.

### Options possibles, avec leurs compromis

**Option A — Assistance rapide Windows (Quick Assist)**
- Avantage : aucune installation, natif sur Windows 10/11, gratuit
- Limite : le client doit savoir chercher "Assistance rapide" dans le menu démarrer — un client peu à l'aise peut buter dès cette étape

**Option B — Outil portable (AnyDesk/TeamViewer QuickSupport)**
- Avantage : fonctionne aussi sur d'anciennes versions de Windows, pas besoin de droits admin
- Limite : nécessite d'envoyer un lien de téléchargement, et que le client sache double-cliquer sur le bon fichier

**Option C — Guidage vocal par téléphone, sans aucune prise de contrôle**
- Avantage : fonctionne toujours, même sans aucun outil
- Limite : lent, dépend de la capacité du client à décrire précisément ce qu'il voit

### Ce qu'un bon technicien retient de cette situation
Le vrai problème n'est pas technique, il est **organisationnel** : ne pas attendre qu'un incident survienne pour découvrir qu'aucune solution n'est prête. Une bonne pratique consiste à toujours avoir un lien portable ou une procédure "Assistance rapide" déjà documentée et prête à envoyer, avant même d'en avoir besoin.

---

## Situation 2 — Un utilisateur signale que "tout est lent", sans autre précision

### Le contexte
Un ticket arrive avec juste : "mon ordinateur est lent". Aucune autre information.

### Le vrai dilemme
Cette description peut correspondre à des dizaines de causes différentes (RAM saturée, disque plein, virus, Windows Update en tâche de fond, mise à jour de pilote défaillante, câble réseau défectueux). Un technicien qui se précipite sur une hypothèse sans vérifier risque de perdre du temps sur la mauvaise piste.

### Options possibles

**Option A — Poser des questions de qualification avant d'intervenir**
Depuis quand ? Sur toutes les applications ou une seule ? Après une mise à jour récente ? Ça oriente immédiatement le diagnostic.

**Option B — Se connecter directement et lancer un diagnostic systématique**
Gestionnaire des tâches → Ressources → isoler CPU/RAM/disque en 2 minutes, sans dépendre de la description de l'utilisateur (parfois peu fiable ou imprécise).

### Ce qu'un bon technicien retient
Les deux options sont complémentaires, pas concurrentes : quelques questions ciblées avant d'intervenir permettent souvent d'aller directement à la bonne case du diagnostic, plutôt que de tout vérifier dans l'ordre à l'aveugle.

---

## Situation 3 — Une alerte de sécurité se déclenche, mais on ne sait pas si c'est une vraie menace

### Le contexte
Un système d'alerte (SIEM, antivirus, EDR) signale une activité suspecte — par exemple, de nombreuses tentatives de connexion échouées sur un serveur.

### Le vrai dilemme
Réagir trop vite (bloquer une IP, couper un service) peut interrompre un usage légitime. Ne pas réagir assez vite peut laisser une vraie attaque se poursuivre.

### Options possibles

**Option A — Bloquer immédiatement, poser les questions ensuite**
Rapide, limite le risque immédiat, mais peut bloquer un utilisateur légitime qui s'est simplement trompé de mot de passe plusieurs fois.

**Option B — Vérifier le contexte avant d'agir** (origine de l'IP, fréquence, cible précise)
Plus sûr sur le fond, mais prend du temps pendant lequel une vraie attaque pourrait continuer.

**Option C — Automatiser une réaction proportionnée** (ex : bannissement temporaire automatique après un seuil précis, plutôt qu'un blocage permanent immédiat)
C'est l'approche mise en place dans ce projet avec fail2ban : un seuil de tentatives déclenche un bannissement d'une durée limitée, pas une décision humaine en urgence à chaud.

### Ce qu'un bon technicien retient
La meilleure réponse à ce dilemme n'est souvent pas de choisir entre "réagir vite" ou "vérifier d'abord", mais de **préparer à l'avance une règle automatique proportionnée**, pour ne pas avoir à trancher dans l'urgence à chaque fois.

---

## Situation 4 — Un appareil mobile géré par le MDM ne répond plus, et on ignore s'il est perdu, volé, ou juste éteint

### Le contexte
Un appareil professionnel disparaît de la liste "en ligne" du MDM. Le responsable ne sait pas si l'utilisateur l'a simplement laissé sans batterie, s'il est en zone sans réseau, ou si l'appareil a été perdu/volé.

### Le vrai dilemme
Déclencher un effacement à distance ("wipe") sans certitude peut détruire des données professionnelles pour rien, si l'appareil réapparaît le lendemain. Attendre trop longtemps en cas de vol réel expose les données de l'entreprise.

### Options possibles

**Option A — Contacter directement l'utilisateur avant toute action**
Le plus sûr, mais suppose qu'on puisse le joindre rapidement par un autre moyen.

**Option B — Verrouiller l'appareil à distance sans l'effacer**
Compromis : bloque l'accès immédiatement, sans perte de données, réversible si l'appareil réapparaît.

**Option C — Attendre un délai défini à l'avance (ex : 48h) avant d'escalader vers un effacement complet**

### Ce qu'un bon technicien retient
Ce genre de décision ne devrait jamais s'improviser en plein incident — une vraie procédure d'entreprise documente à l'avance quel délai et quelle étape appliquer, pour que la décision ne repose pas sur le jugement personnel du technicien de garde ce jour-là.

---

## Situation 5 — Un employé perd son téléphone professionnel un vendredi soir

### Le contexte
Un employé appelle en urgence un vendredi à 19h : il ne retrouve plus son téléphone professionnel, ne sait pas s'il l'a oublié quelque part ou s'il lui a été volé.

### Le vrai dilemme
Le technicien de garde doit décider seul, sous pression et sans toute l'information, entre attendre (au cas où l'appareil réapparaît) et agir immédiatement (effacement à distance).

### Ce qui aide vraiment dans cette situation
Ce n'est pas une meilleure improvisation dans l'instant qui résout ce dilemme, mais une **procédure déjà écrite à l'avance** : par exemple "verrouillage immédiat systématique, effacement après 24h sans nouvelle" — ça retire la pression de décider seul en urgence, et remplace un jugement personnel stressé par une règle déjà validée à froid.

---

## Situation 6 — Un manager demande un accès complet aux messages d'un appareil professionnel, pour surveiller un employé suspecté de faute

### Le contexte
Un manager, inquiet d'un possible comportement problématique d'un employé, demande au technicien d'extraire les messages ou l'historique complet d'un appareil professionnel géré par le MDM.

### Le vrai dilemme
Le technicien a probablement la capacité technique de le faire (selon le MDM), mais cette action soulève une vraie question légale et éthique, largement en dehors du rôle habituel d'un technicien IT.

### Ce qu'un bon technicien retient
La compétence technique ne donne pas automatiquement le droit ou la légitimité d'agir — ce type de demande doit être redirigé vers les RH ou la direction juridique de l'entreprise, jamais traité comme une simple requête technique à exécuter sans questionnement.
