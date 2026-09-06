# Post-mortem — trois échecs MDM avant de comprendre la vraie cause du blocage

## Résumé

Trois solutions MDM différentes ont échoué à l'installation sur une infrastructure cloud en architecture ARM64, avec exactement la même erreur générique (`exec format error`). Après avoir traité les deux premiers échecs comme des cas isolés à contourner, le troisième a poussé à comprendre la vraie cause commune, ce qui a permis de la résoudre définitivement plutôt que de continuer à chercher une quatrième alternative.

## Chronologie

**Tentative 1 — Fleet**
Installation tentée via l'image Docker officielle. Échec immédiat au démarrage du conteneur avec l'erreur `exec format error`. Recherche rapide : aucune image ARM64 n'existe pour ce projet, uniquement x86_64. Abandon, passage à une alternative.

**Tentative 2 — Flyve MDM**
Recherche d'une alternative supposée plus légère. Impossible de trouver une URL de téléchargement fonctionnelle pour le plugin — plusieurs liens menaient à des pages 404. Après vérification plus poussée, découverte que le projet est en réalité abandonné par son éditeur depuis plusieurs années (dépôts archivés officiellement). Abandon, cette fois pour une raison différente (projet mort, pas un problème d'architecture).

**Tentative 3 — Headwind MDM, image officielle**
Troisième tentative, cette fois avec un projet activement maintenu. Installation via l'image Docker officielle publiée par l'éditeur. Même erreur exacte que la tentative 1 : `exec format error`.

**Point de bascule** — à ce stade, le réflexe naturel aurait été de chercher une quatrième alternative MDM. Mais le fait de rencontrer *exactement la même erreur*, sur un projet différent et actif, a soulevé une vraie question : est-ce que le problème vient de chaque projet individuellement, ou d'une cause plus générale liée à l'architecture ARM64 elle-même ?

**Vérification de la cause** — recherche spécifique sur la nature de l'erreur `exec format error` : elle indique qu'un binaire compilé pour une architecture (x86_64) est exécuté sur une architecture différente (ARM64), ce qui est rejeté au niveau du système, pas au niveau de l'application elle-même.

**Vérification du code source de Headwind MDM** : contrairement à Fleet (qui distribue un binaire compilé, sans code source facilement recompilable dans ce contexte), Headwind MDM est écrit en Java, exécuté sur Tomcat — un langage et un serveur d'applications qui ne dépendent pas nativement d'une architecture CPU précise, contrairement à un binaire natif.

**Décision** : plutôt que de télécharger l'image Docker officielle (qui contient un binaire déjà compilé en x86_64), reconstruire cette image soi-même, directement sur la machine ARM64, à partir du Dockerfile source officiel — ce qui force la recompilation pour la bonne architecture.

**Vérification d'un blocage potentiel** : le Dockerfile installait un paquet nommé `aapt` (outil d'analyse de fichiers Android), habituellement source de blocage sur ARM. Vérification que ce paquet existe bien en version ARM64 sur la distribution Linux utilisée par l'image (Ubuntu 22.04) — confirmé disponible.

**Exécution** : `docker build` lancé directement sur la VM ARM64. Succès, sans aucune erreur.

**Résultat** : l'image reconstruite localement démarre correctement, le service devient accessible et fonctionnel.

## Cause racine

L'erreur n'était pas propre à Headwind MDM ni à Fleet séparément — elle venait du fait que les deux projets ne distribuent officiellement qu'une image Docker **pré-compilée pour x86_64**, sans variante ARM64. Pour Fleet, cette limite est réelle et non contournable facilement (pas de recompilation simple documentée). Pour Headwind MDM, cette limite ne concernait que l'image publiée, pas le code source lui-même, qui n'avait aucune dépendance réelle à une architecture précise.

## Ce qui a permis de trouver la vraie cause

Le vrai tournant n'a pas été une recherche plus approfondie sur Headwind MDM spécifiquement, mais le fait de **remarquer une répétition** : la même erreur exacte sur deux projets différents a changé la question posée, de "pourquoi ce projet précis échoue" à "qu'est-ce que cette erreur signifie techniquement, en général". Cette généralisation a immédiatement orienté vers la vraie cause (architecture du binaire), plutôt que vers une hypothèse propre à chaque outil.

## Ce qui a été changé pour éviter que ça se reproduise

- Avant de tester un nouvel outil sur cette infrastructure ARM64, vérification systématique et rapide : le projet distribue-t-il uniquement une image Docker pré-compilée, ou permet-il une reconstruction depuis le code source ?
- Documentation de cette démarche (reconstruction locale) comme solution de premier recours face à un `exec format error`, avant même d'envisager une alternative complètement différente

## Ce que ça m'a appris, plus largement

Face à une même erreur qui se répète sur des contextes différents, la bonne réaction n'est pas de traiter chaque cas comme un problème isolé à contourner un par un, mais de se demander si une cause commune, plus générale, explique tout à la fois. Ça change complètement la stratégie de résolution — d'une recherche au cas par cas vers une vraie compréhension, réutilisable pour n'importe quel prochain outil rencontrant la même limite.
