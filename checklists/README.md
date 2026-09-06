# ✅ Checklists rapides

Contrairement au reste du guide, ces checklists ne servent pas à comprendre en profondeur, mais à **agir vite** en plein incident — à consulter en 30 secondes, pas à lire en entier.

---

## 🖥️ Nouvel employé — poste à préparer

- [ ] Créer le compte utilisateur (AD, email, applications internes)
- [ ] Vérifier que l'appareil est bien enrôlé dans le MDM avant remise
- [ ] Appliquer le profil de configuration standard (pas une config manuelle ad hoc)
- [ ] Installer les applications professionnelles nécessaires
- [ ] Vérifier l'accès VPN si le poste est destiné au télétravail
- [ ] Tester une connexion complète avant remise (login, email, accès réseau)
- [ ] Remettre les identifiants par un canal séparé du poste lui-même (jamais un post-it collé dessus)
- [ ] Planifier un point de suivi à J+3 pour vérifier qu'aucun blocage n'est apparu après la prise en main

---

## 🔄 Poste qui redémarre en boucle

- [ ] Noter le code d'erreur exact affiché (s'il y en a un)
- [ ] Démarrer en mode sans échec pour isoler un problème logiciel
- [ ] Vérifier les derniers événements avant le premier redémarrage (Observateur d'événements / journalctl)
- [ ] Vérifier si un pilote ou une mise à jour a été installé juste avant le problème
- [ ] Tester la RAM si aucune cause logicielle claire n'apparaît
- [ ] Vérifier la santé du disque (SMART) avant d'exclure une cause matérielle

---

## 🚨 Suspicion de ransomware — premières minutes

- [ ] Isoler immédiatement la machine du réseau (câble débranché, Wi-Fi coupé)
- [ ] Ne pas éteindre la machine si une analyse est envisagée (sauf chiffrement visiblement encore actif)
- [ ] Identifier les dossiers réseau partagés accessibles depuis ce poste
- [ ] Vérifier si les sauvegardes disponibles sont elles-mêmes saines (pas déjà touchées)
- [ ] Ne jamais décider seul de payer une rançon — remonter à la direction immédiatement
- [ ] Documenter l'heure exacte de détection et les premiers symptômes observés

---

## 📱 Téléphone professionnel signalé perdu

- [ ] Tenter une localisation à distance en premier (avant toute action irréversible)
- [ ] Verrouiller l'appareil à distance (pas encore l'effacer)
- [ ] Faire sonner l'appareil si probablement à proximité
- [ ] Attendre le délai défini par la politique de l'entreprise avant d'escalader
- [ ] Si le délai est dépassé sans nouvelle : effacement à distance + révocation des accès associés

---

## 🌐 Aucun accès réseau signalé par un utilisateur

- [ ] Vérifier le câble/Wi-Fi en premier (souvent la cause la plus simple)
- [ ] Vérifier si une adresse IP a bien été attribuée (pas de 169.254.x.x)
- [ ] Tester la connectivité brute par IP avant de blâmer le DNS
- [ ] Vérifier si le problème touche un seul utilisateur ou toute une zone
- [ ] Si toute une zone est touchée : vérifier le VLAN et le DHCP avant d'aller plus loin

---

## 📧 Email de phishing signalé par un utilisateur

- [ ] Ne pas cliquer sur les liens/pièces jointes pour "vérifier"
- [ ] Vérifier l'en-tête complet de l'email (pas juste l'adresse affichée)
- [ ] Vérifier si d'autres employés ont reçu le même email
- [ ] Bloquer l'expéditeur au niveau de la passerelle email, pas juste chez l'utilisateur
- [ ] Si l'utilisateur a cliqué : traiter immédiatement comme un poste potentiellement compromis

---

## 🖨️ Imprimante réseau injoignable

- [ ] Ping vers l'imprimante avant toute autre vérification
- [ ] Vérifier que le spouleur d'impression est bien démarré
- [ ] Vider la file d'attente si un document semble bloqué depuis longtemps
- [ ] Vérifier que le pilote correspond exactement au bon modèle
- [ ] Marquer l'imprimante "en ligne" manuellement si elle est restée bloquée "hors ligne" après une coupure

---

## 🔑 Utilisateur qui a oublié son mot de passe

- [ ] Vérifier l'identité de la personne avant toute réinitialisation (jamais par simple email, un appel ou une vérification en personne reste préférable)
- [ ] Réinitialiser via la procédure officielle (AD, portail self-service si disponible)
- [ ] Forcer un changement de mot de passe à la prochaine connexion
- [ ] Vérifier si le compte a été verrouillé suite à plusieurs tentatives échouées (pas juste un oubli simple)
- [ ] Rappeler la politique de mot de passe en vigueur si le nouveau choix est trop faible

---

## 💻 Poste très lent signalé par un utilisateur

- [ ] Demander depuis quand, et si c'est général ou lié à une application précise
- [ ] Vérifier CPU, RAM, disque dans le gestionnaire de tâches avant toute autre action
- [ ] Vérifier si une mise à jour Windows est en cours en arrière-plan
- [ ] Vérifier l'espace disque disponible (un disque presque plein ralentit fortement le système)
- [ ] Vérifier les programmes au démarrage si la lenteur survient dès l'ouverture de session

---

## 🔌 VPN qui ne se connecte pas ou coupe sans cesse

- [ ] Vérifier la connectivité internet de base avant de blâmer le VPN
- [ ] Vérifier si un conflit d'adresses IP existe entre réseau local et réseau distant
- [ ] Redémarrer l'adaptateur réseau virtuel du VPN
- [ ] Vérifier les logs du client VPN pour un message d'erreur précis
- [ ] Tester depuis un autre réseau (partage de connexion mobile) pour isoler si le problème vient du réseau local de l'utilisateur

---

## 🦠 Poste avec comportement suspect (potentiellement compromis)

- [ ] Isoler du réseau avant toute autre action
- [ ] Ne pas réinstaller immédiatement sans avoir noté les éléments suspects observés
- [ ] Vérifier les connexions réseau actives et les processus associés
- [ ] Changer les mots de passe utilisés récemment depuis ce poste
- [ ] Vérifier si d'autres postes du même réseau montrent des signes similaires

---

## 📞 Appel se présentant comme "le support informatique" (vishing suspecté)

- [ ] Ne jamais donner d'information sensible pendant l'appel, quelle que soit l'urgence annoncée
- [ ] Raccrocher et rappeler soi-même le numéro officiel connu
- [ ] Vérifier si d'autres personnes de l'entreprise ont reçu un appel similaire
- [ ] Signaler l'appel même s'il n'a mené à aucune divulgation d'information

---

## 🗂️ Migration ou remplacement d'un poste de travail

- [ ] Sauvegarder les données de l'utilisateur avant toute chose
- [ ] Vérifier la liste des applications installées sur l'ancien poste (souvent oubliées si non documentées)
- [ ] Récupérer les paramètres réseau spécifiques (imprimantes, lecteurs réseau, VPN)
- [ ] Tester une connexion complète sur le nouveau poste avant de considérer la migration terminée
- [ ] Ne désactiver/récupérer l'ancien poste qu'après confirmation explicite de l'utilisateur

---

## 🖥️ Serveur qui devient injoignable soudainement

- [ ] Vérifier d'abord si le serveur répond encore en local (ping depuis une autre machine du même réseau)
- [ ] Vérifier la charge CPU/RAM/disque avant de suspecter un problème réseau
- [ ] Vérifier les logs système autour de l'heure exacte de la panne
- [ ] Vérifier si un service critique a redémarré ou crashé (systemctl status, journalctl)
- [ ] Vérifier les deux niveaux de pare-feu si applicable (système local + réseau/cloud) avant de conclure à une panne matérielle

---

## 🔐 Compte utilisateur potentiellement compromis (connexion suspecte détectée)

- [ ] Désactiver le compte immédiatement, pas seulement changer le mot de passe
- [ ] Forcer la déconnexion de toutes les sessions actives
- [ ] Vérifier l'historique récent : emails envoyés, règles de transfert automatique ajoutées, fichiers partagés
- [ ] Réactiver uniquement après vérification complète, avec double authentification si possible
- [ ] Informer l'utilisateur concerné du changement, sans attendre qu'il le découvre lui-même

---

## 📲 Application professionnelle qui ne s'installe pas sur un appareil géré par MDM

- [ ] Vérifier que l'application est bien associée au bon groupe/configuration
- [ ] Vérifier l'espace de stockage disponible sur l'appareil
- [ ] Vérifier si l'installation est limitée au Wi-Fi uniquement, et si l'appareil était bien connecté en Wi-Fi au moment voulu
- [ ] Vérifier la compatibilité de version d'OS entre l'application et l'appareil
- [ ] Consulter l'historique de tentatives d'installation dans la console MDM pour un message d'erreur précis

---

## 🔥 Incident de sécurité — première communication à envoyer

- [ ] Rester factuel, éviter tout jugement ou accusation prématurée
- [ ] Préciser ce qui est confirmé, et ce qui est encore en cours de vérification (ne jamais présenter une hypothèse comme un fait établi)
- [ ] Indiquer une action concrète déjà prise (isolation, changement de mot de passe) plutôt que seulement "on regarde le problème"
- [ ] Préciser qui contacter en cas de nouvelle observation liée à l'incident
- [ ] Prévoir un point de suivi à heure fixe, même sans nouvelle information à ce moment-là
