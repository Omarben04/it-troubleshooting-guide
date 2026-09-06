# IT Troubleshooting Guide

Guide complet de résolution des problèmes IT courants, organisé par système d'exploitation et par domaine — construit à partir de vraies pannes rencontrées ou observées en contexte professionnel, avec la méthode de diagnostic étape par étape, pas juste la solution finale.

## Pourquoi ce projet

Un bon technicien IT ne se contente pas de connaître des solutions toutes faites — il sait diagnostiquer méthodiquement, en isolant les variables une par une. Ce guide documente cette démarche pour les pannes les plus fréquentes, sur Windows, Linux, macOS, et sur la gestion mobile/MDM — et sert aussi de référence personnelle à consulter en cas de vrai besoin.

## Structure

- [`windows/`](windows/README.md) — pannes courantes Windows (réseau, performance, services, VPN, Active Directory, pilotes)
- [`linux/`](linux/README.md) — pannes courantes Linux, avec distinctions Debian/Ubuntu vs RedHat/CentOS/Oracle Linux
- [`macos/`](macos/README.md) — pannes courantes macOS
- [`mobile-mdm/`](mobile-mdm/README.md) — problèmes mobiles et gestion de flotte (MDM) : enrôlement, appareil perdu/volé, BYOD, confidentialité
- [`scenarios-reels.md`](scenarios-reels.md) — situations réelles racontées avec leur dilemme et plusieurs options possibles, pas juste une solution technique isolée

## Méthode générale de diagnostic

Face à n'importe quel incident, la démarche reste la même, quel que soit l'OS :

1. **Isoler le périmètre** : le problème touche-t-il un seul utilisateur, un groupe, ou tout le monde ?
2. **Vérifier ce qui a changé récemment** : mise à jour, nouvelle installation, changement réseau
3. **Reproduire si possible** : le problème est-il systématique ou intermittent ?
4. **Consulter les logs avant de toucher à quoi que ce soit**
5. **Isoler une variable à la fois** : ne jamais changer plusieurs choses en même temps
