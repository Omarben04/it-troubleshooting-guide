# 🎯 Bonnes pratiques d'un technicien IT

Ce qui distingue souvent un bon technicien d'un simple exécutant de solutions, ce n'est pas la quantité de connaissances techniques, mais une série de réflexes et d'attitudes répétés au quotidien.

---

## Communication avec les utilisateurs

**Ne jamais faire sentir à quelqu'un qu'il est "bête" d'avoir un problème.** La personne en face n'a pas les mêmes connaissances techniques, et la juger ou la faire sentir incompétente ne résout rien — ça crée juste de la méfiance pour la prochaine fois qu'elle aura besoin d'aide.

**Expliquer simplement ce qu'on fait, sans jargon inutile.** "Je vérifie si votre ordinateur reçoit bien une adresse sur le réseau" plutôt que "je regarde si le DHCP a bien attribué un bail" — sauf si la personne en face est elle-même technique.

**Donner un délai réaliste, pas optimiste.** Annoncer "ce sera réglé dans 10 minutes" puis prendre une heure abîme la confiance bien plus que d'annoncer honnêtement "ça va prendre du temps, je vous tiens au courant".

**Confirmer la résolution avec l'utilisateur lui-même**, pas seulement supposer que "ça devrait marcher maintenant" — beaucoup d'incidents rouvrent parce que personne n'a vérifié concrètement avec la personne concernée.

---

## Méthode de travail

**Ne jamais changer plusieurs choses à la fois.** Si trois hypothèses semblent plausibles, les tester une par une — sinon, en cas de résolution, on ne sait jamais laquelle était la vraie cause, et le problème reviendra un jour sans qu'on sache pourquoi il avait disparu.

**Toujours vérifier ce qui a changé récemment avant de chercher plus loin.** La cause la plus fréquente d'un problème soudain est un changement récent (mise à jour, nouvelle installation, modification de configuration), pas une cause mystérieuse.

**Documenter en même temps qu'on résout, pas après.** Noter les commandes utilisées et les observations pendant le diagnostic, pas de mémoire une fois le problème réglé — les détails s'oublient vite, et une intervention non documentée n'aide personne la prochaine fois qu'elle se reproduit.

**Ne jamais supposer qu'un service "actif" fait ce qu'on attend de lui.** Un service qui tourne sans erreur n'est pas la même chose qu'un service qui fonctionne comme prévu — toujours vérifier le résultat concret, pas juste le statut affiché.

---

## Sécurité et rigueur

**Ne jamais donner l'impression que la sécurité ralentit le travail.** Une bonne pratique de sécurité expliquée clairement (pourquoi ce mot de passe doit être plus complexe, pourquoi cette pièce jointe est suspecte) est mieux acceptée qu'une règle imposée sans explication.

**Toujours vérifier deux fois avant une action irréversible** (suppression de données, effacement à distance d'un appareil, désactivation d'un compte) — la rapidité n'a pas de valeur si elle mène à une erreur qui ne peut plus être corrigée.

**Ne jamais partager un mot de passe ou un accès par un canal non sécurisé**, même sous la pression du temps — un mot de passe envoyé "vite fait" par un message non chiffré peut rester accessible bien plus longtemps que l'urgence qui l'a motivé.

**Savoir dire non ou remonter la question**, quand une demande dépasse le rôle technique (par exemple, accéder aux messages personnels d'un employé sur simple demande d'un manager) — la compétence technique ne donne pas automatiquement la légitimité d'agir.

---

## Face à l'échec ou à un problème non résolu

**Accepter de changer de stratégie plutôt que de s'acharner indéfiniment.** Passer des heures sur une seule piste qui ne mène nulle part coûte souvent plus cher que de basculer vers une alternative après un temps raisonnable de diagnostic.

**Ne jamais cacher une erreur qu'on a soi-même causée.** La signaler rapidement permet souvent de la corriger avant qu'elle n'aggrave la situation — la découvrir plus tard, une fois les conséquences étendues, coûte toujours plus cher en confiance et en réparation.

**Transformer chaque incident en apprentissage, pas juste en solution ponctuelle.** Un problème résolu sans comprendre sa vraie cause a de bonnes chances de revenir, sous une forme légèrement différente.

---

## Organisation personnelle

**Garder une trace des solutions déjà trouvées.** Un problème rencontré une fois se reproduira presque toujours — un vrai technicien construit progressivement sa propre base de connaissances, plutôt que de tout rechercher à nouveau à chaque fois.

**Se tenir informé, sans pour autant tout suivre en permanence.** Une vulnérabilité critique largement médiatisée mérite d'être vérifiée sur son propre périmètre rapidement, sans attendre qu'un incident la révèle a posteriori.

**Prendre le temps de vérifier ses propres décisions passées.** Une configuration mise en place il y a un an peut ne plus être adaptée — revenir de temps en temps sur ce qui a été fait, pas seulement avancer sur de nouvelles tâches.

---

## Gestion des priorités

**Urgent et important ne sont pas la même chose.** Un serveur de production en panne est urgent ET important. Un utilisateur qui demande un changement de fond d'écran est ni l'un ni l'autre, même s'il insiste. Savoir distinguer les deux évite de traiter les tickets uniquement par ordre d'arrivée.

**Communiquer un délai réaliste, même pour ce qu'on ne traite pas tout de suite.** "Je m'en occupe après avoir réglé la panne serveur, d'ici ce début d'après-midi" vaut toujours mieux que le silence, même pour une demande secondaire.

**Ne jamais laisser une urgence de sécurité attendre derrière une simple gêne de confort**, même si cette dernière est arrivée en premier dans la file d'attente.

---

## Travail en équipe et passation

**Transmettre un incident en cours avec un vrai résumé, pas juste "je n'ai pas eu le temps".** Ce qui a déjà été vérifié, ce qui reste à tester, et pourquoi certaines pistes ont été écartées — sans ça, la personne qui reprend perd du temps à refaire ce qui a déjà été fait.

**Ne jamais supposer que la personne précédente a forcément tout bien noté.** Vérifier soi-même les bases (le problème est-il toujours d'actualité, rien n'a changé depuis) avant de continuer directement sur les notes d'un collègue.

**Partager une découverte utile, même si le problème est déjà résolu de son côté.** Une solution trouvée pour soi-même profite souvent à toute une équipe si elle est documentée et partagée, pas juste gardée pour la prochaine fois où on en aura besoin personnellement.

---

## Savoir quand escalader

**Reconnaître la limite de ses propres droits d'accès, pas seulement de ses compétences.** Certaines actions (accès à des données sensibles, modification d'une infrastructure critique) nécessitent une validation, même si on sait techniquement comment faire.

**Demander de l'aide n'est pas un aveu d'échec.** Un technicien qui s'acharne seul pendant des heures sur un problème qu'un collègue plus expérimenté résoudrait en quelques minutes fait perdre du temps à tout le monde, y compris à l'utilisateur qui attend une solution.

**Escalader tôt en cas de doute sur la légitimité d'une demande**, pas seulement en cas de difficulté technique — une demande inhabituelle (accès à des données personnelles d'un employé, action qui semble dépasser une simple maintenance) mérite d'être validée avant d'être exécutée, même si elle vient d'une personne haut placée dans la hiérarchie.
