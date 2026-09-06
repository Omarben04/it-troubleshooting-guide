# IT Troubleshooting Guide

Guide complet de résolution des problèmes IT courants, organisé par système d'exploitation et par domaine — construit à partir de vraies pannes rencontrées ou observées en contexte professionnel, avec la méthode de diagnostic étape par étape, pas juste la solution finale.

## Pourquoi ce projet

Un bon technicien IT ne se contente pas de connaître des solutions toutes faites — il sait diagnostiquer méthodiquement, en isolant les variables une par une, et sait réagir avec la bonne proportion face à un incident de sécurité. Ce guide documente cette démarche pour les pannes les plus fréquentes, et sert aussi de référence personnelle à consulter en cas de vrai besoin.

## Sommaire

### Par système d'exploitation
- [Windows](windows/README.md) — réseau, performance, BSOD, Windows Update, imprimante/scanner, disque, profil utilisateur, VPN, Active Directory, pilotes, certificats
- [Linux](linux/README.md) — serveurs, services, réseau, pare-feu (ufw/firewalld), permissions/SELinux, SSH, fail2ban, Docker, paquets (apt/dnf), VPN, sauvegardes — avec distinctions Debian/Ubuntu vs RedHat/CentOS/Oracle Linux
- [macOS](macos/README.md) — applications, réseau Wi-Fi, disque, démarrage, VPN, Time Machine

### Par domaine
- [Mobile & MDM](mobile-mdm/README.md) — enrôlement, appareil perdu/volé, mot de passe oublié, BYOD, applications, confidentialité
- [Sécurité](securite/README.md) — phishing, ransomware, poste compromis, brute-force, MITM, DDoS, zero-day, vishing, sécurité mobile (app malveillante, faux Wi-Fi, smishing)
- [Situations réelles](scenarios-reels.md) — dilemmes concrets avec plusieurs options possibles, pas juste une solution technique isolée

## Méthode générale de diagnostic

Face à n'importe quel incident, la démarche reste la même, quel que soit l'OS :

1. **Isoler le périmètre** : le problème touche-t-il un seul utilisateur, un groupe, ou tout le monde ?
2. **Vérifier ce qui a changé récemment** : mise à jour, nouvelle installation, changement réseau
3. **Reproduire si possible** : le problème est-il systématique ou intermittent ?
4. **Consulter les logs avant de toucher à quoi que ce soit**
5. **Isoler une variable à la fois** : ne jamais changer plusieurs choses en même temps

## Principe général face à un incident de sécurité

1. **Contenir** — empêcher la propagation avant tout
2. **Préserver les preuves** — ne rien effacer avant d'avoir compris
3. **Analyser** — comprendre l'étendue réelle, pas supposer
4. **Communiquer** — informer les bonnes personnes au bon moment
5. **Corriger** — nettoyer une fois l'analyse terminée
6. **Tirer les leçons** — documenter pour éviter la récidive
