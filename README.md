# 🛠️ IT Troubleshooting Guide

![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![macOS](https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white)
![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)
![Security](https://img.shields.io/badge/Sécurité-D32F2F?style=for-the-badge&logo=shield&logoColor=white)
![Network](https://img.shields.io/badge/Réseau-1976D2?style=for-the-badge&logo=cisco&logoColor=white)

Un peu d'histoire derrière ce dépôt : je le tiens à jour au fil de ce que je rencontre vraiment, pas comme une liste théorique. Chaque panne ici, je l'ai soit vécue, soit vue racontée par quelqu'un du métier, avec la vraie méthode pour s'en sortir.

## Pourquoi ce projet existe

Un bon technicien ne connaît pas toutes les solutions par cœur. Il sait diagnostiquer méthodiquement, isoler une variable à la fois, et surtout garder son calme et réagir de façon proportionnée face à un vrai incident de sécurité. C'est cette démarche que j'essaie de documenter ici — pas juste "voici la commande", mais pourquoi et dans quel ordre.

---

## 📁 Par système d'exploitation

| | Domaine couvert |
|---|---|
| 🪟 **[Windows](windows/README.md)** | Réseau, BSOD, Windows Update, imprimante/scanner, disque, profil utilisateur, VPN, Active Directory, pilotes, certificats |
| 🐧 **[Linux](linux/README.md)** | Serveurs, services, réseau, pare-feu (ufw/firewalld), SELinux, SSH, fail2ban, Docker, paquets — avec les vraies différences entre Debian/Ubuntu et RedHat/CentOS |
| 🍎 **[macOS](macos/README.md)** | Applications, Wi-Fi, disque, démarrage, VPN, Time Machine |

## 📱 Par domaine

| | Domaine couvert |
|---|---|
| 📲 **[Mobile & MDM](mobile-mdm/README.md)** | Enrôlement, appareil perdu ou volé, mot de passe oublié, BYOD, confidentialité |
| 🔒 **[Sécurité](securite/README.md)** | Phishing, ransomware, poste compromis, brute-force, DDoS, zero-day, vishing, et toute la partie mobile (app malveillante, faux Wi-Fi, smishing) |
| 🌐 **[Réseau approfondi](reseau/README.md)** | DNS, DHCP, VLAN, sous-réseaux/masques, NAT, pare-feu en couches, un vrai cas de dépannage complexe |
| 💬 **[Situations vécues](scenarios-reels.md)** | Des vrais dilemmes, racontés avec plusieurs options possibles — pas une seule réponse toute faite |
| 📝 **[Post-mortems](post-mortems/)** | Des cas réels racontés en entier, du symptôme à la cause profonde |

---

## 🧭 Ma méthode de diagnostic, avant tout

Peu importe l'OS, je me pose toujours les mêmes questions dans cet ordre :

1. Est-ce que ça touche une seule personne, un groupe, ou tout le monde ?
2. Qu'est-ce qui a changé récemment ? Une mise à jour, une install, un changement réseau ?
3. Est-ce que ça se reproduit systématiquement, ou c'est intermittent ?
4. Je regarde les logs avant de toucher à quoi que ce soit
5. Je change une seule chose à la fois — jamais deux en même temps

## 🚨 Face à un incident de sécurité

Ma logique reste toujours la même :

**Contenir** d'abord → **préserver** ce qui pourrait servir de preuve → **analyser** vraiment, sans supposer → **prévenir** les bonnes personnes, ni trop tôt ni trop tard → **corriger** une fois qu'on a compris → et **en tirer une leçon**, pour que ça ne se reproduise pas pareil.
