# Post-mortem — fail2ban configuré mais ne bannit aucune IP malgré des attaques visibles

## Résumé

fail2ban était installé et actif sur un serveur exposé publiquement, avec des dizaines de tentatives de connexion SSH suspectes visibles dans les logs chaque minute. Pourtant, après plusieurs heures d'activité, aucune IP n'avait été bannie. Le service semblait fonctionner (actif, sans erreur), ce qui rendait le diagnostic initial trompeur.

## Chronologie

**T+0** — Installation de fail2ban et configuration d'une règle basique pour surveiller les connexions SSH (`maxretry = 3`, `bantime = 3600`).

**T+5 minutes** — Vérification du statut avec `fail2ban-client status sshd` : le service tourne, aucune erreur au démarrage.

**T+30 minutes** — Les logs système montrent clairement des dizaines de tentatives de connexion SSH échouées, provenant des mêmes adresses IP répétées. Nouvelle vérification du statut fail2ban : toujours `Currently banned: 0`, `Total failed: 0`.

**T+40 minutes** — Première hypothèse : le fichier de log surveillé n'est peut-être pas le bon. Vérification de la configuration (`logpath`) : correcte, pointe bien vers le fichier où les tentatives sont visibles.

**T+50 minutes** — Deuxième hypothèse : fail2ban utilise peut-être le journal système (journalctl) par défaut plutôt que le fichier de log configuré, sur cette distribution précise. Ajout du paramètre `backend = polling` pour forcer la lecture directe du fichier.

**T+55 minutes** — Redémarrage du service. Toujours aucune tentative comptabilisée après plusieurs minutes d'observation, malgré des tentatives visibles en continu dans les logs bruts.

**T+70 minutes** — Décision de tester directement le filtre utilisé par fail2ban avec l'outil `fail2ban-regex`, qui permet de vérifier si un filtre reconnaît vraiment les lignes d'un fichier de log donné, sans attendre un vrai déclenchement.

**T+75 minutes** — Résultat de `fail2ban-regex` : le filtre reconnaissait bien le motif des tentatives ("Connection closed by authenticating user... [preauth]"), mais le classait explicitement comme `<F-NOFAIL>` — un marqueur interne qui dit à fail2ban "ce type de message ne doit jamais être compté comme un échec", car ce motif peut aussi correspondre à une déconnexion normale et non malveillante.

**T+80 minutes** — Changement du filtre utilisé, en passant au mode `aggressive` du filtre sshd standard, qui inclut ce motif précis comme une tentative suspecte à comptabiliser (contrairement au mode par défaut, plus permissif pour éviter les faux positifs).

**T+90 minutes** — Redémarrage du service. En quelques minutes, `fail2ban-client status sshd` affiche `Currently banned: 2`, avec les deux adresses IP qui apparaissaient en boucle dans les logs.

## Cause racine

Le filtre standard de fail2ban est volontairement conservateur : certains messages de déconnexion SSH ("Connection closed... [preauth]") peuvent correspondre à une vraie tentative malveillante, mais aussi à une simple déconnexion normale sans rapport avec une attaque. Pour éviter de bannir des utilisateurs légitimes par erreur, le filtre par défaut ignore ce motif précis. Dans ce cas, le trafic observé était réellement malveillant (scans automatiques répétés), mais le filtre par défaut n'avait aucun moyen de le distinguer d'un cas bénin sans configuration supplémentaire.

## Ce qui a permis de trouver la vraie cause

Ce n'est pas d'avoir cherché plus fort dans la documentation générale, mais d'avoir utilisé l'outil de test dédié (`fail2ban-regex`) qui simule exactement ce que fait fail2ban sur un vrai fichier de log, sans attendre passivement qu'un vrai bannissement se produise ou non. Ça a permis de voir concrètement *pourquoi* une ligne n'était pas comptée, plutôt que de simplement constater qu'elle ne l'était pas.

## Ce qui a été changé pour éviter que ça se reproduise

- Documentation systématique du mode de filtre utilisé (`aggressive` vs standard) dans la configuration versionnée du projet, avec la raison de ce choix expliquée en commentaire
- Réflexe ajouté : en cas de service de sécurité qui semble actif mais n'agit jamais, tester directement l'outil de simulation/diagnostic dédié avant de chercher ailleurs

## Ce que ça m'a appris, plus largement

Un service "actif sans erreur" ne veut pas dire "un service qui fait ce qu'on attend de lui". La bonne question n'était pas "pourquoi fail2ban ne marche pas", mais "que fait fail2ban exactement avec cette ligne précise" — une nuance qui change complètement la façon de chercher la solution.
