# Mobile & MDM — Pannes courantes

Section dédiée à la gestion de flotte mobile (MDM), particulièrement pertinente dans un contexte d'entreprise spécialisée en sécurité mobile.

---

## Panne 1 — Appareil qui n'apparaît plus "en ligne" dans le MDM

**Diagnostic** :
1. Vérifier que l'appareil a bien une connexion internet active (Wi-Fi ou données mobiles)
2. Vérifier que l'agent MDM (l'application installée sur l'appareil) n'a pas été désinstallée ou désactivée manuellement par l'utilisateur
3. Vérifier les paramètres de batterie — certains fabricants (notamment Xiaomi, Huawei) tuent agressivement les applications en arrière-plan pour économiser la batterie, ce qui coupe la communication avec le serveur MDM

**Solution courante Android** :

Paramètres → Batterie → Optimisation de la batterie → Trouver l'app MDM → Ne pas optimiser


**Vérifier côté serveur MDM** (exemple avec Headwind MDM) : consulter les logs de communication MQTT, qui doivent montrer les dernières connexions de l'appareil.

---

## Panne 2 — Impossible d'enrôler un nouvel appareil dans le MDM

**Diagnostic** :
1. Vérifier que l'appareil a bien accès à internet pendant la procédure d'enrôlement
2. Vérifier la version d'Android/iOS — certaines versions de MDM ne supportent pas les versions d'OS les plus récentes ou les plus anciennes
3. Sur Android, vérifier si l'appareil nécessite un mode spécifique (Android Enterprise, Device Owner) qui exige parfois une réinitialisation d'usine avant le premier enrôlement

**Cas fréquent** : un appareil déjà configuré avec un compte Google (Android) ne peut pas devenir "Device Owner" — il faut réinitialiser l'appareil avant l'enrôlement pour ce mode spécifique.

---

## Panne 3 — Politique de sécurité (mot de passe, restrictions) qui ne s'applique pas

**Diagnostic** :
1. Vérifier que l'appareil a bien synchronisé sa configuration récemment (voir la dernière communication dans le MDM)
2. Vérifier qu'aucune configuration locale sur l'appareil n'entre en conflit avec la politique poussée par le MDM
3. Forcer une resynchronisation manuelle depuis la console MDM

**Vérifier dans Headwind MDM** : onglet Configurations → vérifier que le bon groupe/configuration est bien assigné à l'appareil concerné (une erreur fréquente est un appareil resté associé à une ancienne configuration).

---

## Panne 4 — Appareil volé ou perdu — procédure d'urgence

**Actions immédiates depuis le MDM** :
1. Verrouillage à distance de l'appareil
2. Effacement à distance des données professionnelles (wipe complet ou sélectif si le MDM le permet)
3. Révocation des accès (certificats, comptes) associés à cet appareil

**Bonne pratique** : documenter et tester cette procédure *avant* qu'elle ne soit nécessaire en urgence — un vrai incident n'est pas le moment d'apprendre à utiliser cette fonctionnalité.

---

## Panne 5 — Notifications MDM qui n'arrivent pas / latence de communication

**Diagnostic** :
Le protocole utilisé pour la communication temps réel (souvent MQTT) nécessite un port spécifique ouvert (par exemple le port 31000 pour Headwind MDM).

```bash
# Côté serveur, vérifier que le port est bien à l'écoute
sudo ss -tlnp | grep 31000
```

**Cas fréquent** : un pare-feu d'entreprise (côté réseau du client, pas du serveur) bloque ce port spécifique, empêchant la communication temps réel malgré un serveur MDM parfaitement fonctionnel.

---

## Panne 6 — Application professionnelle qui ne s'installe pas via le MDM

**Diagnostic** :
1. Vérifier que l'application est bien listée et associée à la bonne configuration/groupe dans le MDM
2. Vérifier l'espace de stockage disponible sur l'appareil
3. Vérifier la connexion internet au moment de l'installation (certains MDM limitent le téléchargement au Wi-Fi uniquement, configurable)

**Vérifier dans Headwind MDM** : onglet Applications → statut d'installation par appareil, visible avec un code couleur (installé, en attente, échec).

---

## Étude de cas — Intervention à distance sur un appareil client sans accès distant préconfiguré

### Contexte
Un client signale un problème sur son poste ou son appareil, mais aucun outil de prise en main à distance n'est déjà en place, et le client n'est pas nécessairement à l'aise techniquement pour en installer un lui-même dans l'urgence.

### Solutions possibles, du plus simple au plus technique

**1. Outils d'assistance sans installation préalable**
- **Windows** : Assistance rapide (Quick Assist), native sur Windows 10/11 — le client génère un code, le technicien s'y connecte, aucune installation requise
- **Mobile (Android/iOS)** : certains MDM proposent une fonctionnalité de partage d'écran à distance intégrée à l'agent déjà installé (si l'appareil est déjà enrôlé) — sinon, un partage d'écran natif via une messagerie déjà installée (WhatsApp, Teams) permet au moins de visualiser le problème

**2. Solutions portables (exécutable unique, sans installation complète)**
AnyDesk ou TeamViewer QuickSupport en version portable — le client télécharge et exécute un seul fichier, sans procédure d'installation ni droits administrateur.

**3. Si rien n'est possible techniquement**
Guidage vocal étape par étape par téléphone, en demandant au client de décrire précisément ce qu'il voit à l'écran — plus lent, mais fonctionne dans toutes les situations.

### Ce que cette situation apprend
Elle souligne l'importance d'anticiper ce type de blocage avant qu'il ne survienne en plein incident — par exemple, en s'assurant qu'un outil léger et sans installation est toujours identifié et prêt à proposer, plutôt que de chercher une solution dans l'urgence pendant que le client attend une réponse.

---

## Panne 7 — Mot de passe de l'appareil oublié (téléphone verrouillé par l'utilisateur)

**Diagnostic** : distinguer deux cas très différents.

**Cas A — L'appareil est enrôlé dans le MDM avec les droits suffisants**
La plupart des MDM permettent un déverrouillage à distance ou une réinitialisation du code de verrouillage sans effacer l'appareil.
Dans Headwind MDM : action "Réinitialiser le mot de passe" disponible depuis la fiche de l'appareil, à condition que la politique de l'appareil autorise cette action.

**Cas B — L'appareil n'est pas enrôlé, ou l'agent MDM a perdu la communication**
Aucune solution à distance possible — il faut soit les identifiants du compte associé (Google pour Android, Apple ID pour iOS) pour déverrouiller via leur procédure officielle, soit une réinitialisation d'usine (avec perte de données si aucune sauvegarde).

---

## Panne 8 — Téléphone professionnel oublié (perdu quelque part, pas volé)

### Le dilemme
Contrairement à un vol confirmé, ici l'appareil pourrait réapparaître dans les heures qui suivent — agir trop vite (effacement complet) peut être disproportionné.

### Démarche recommandée, dans l'ordre
1. **Localiser** : si le MDM ou le compte associé (Google Find My Device, Apple Find My) permet la géolocalisation, l'utiliser en premier
2. **Verrouiller à distance** (pas effacer) : bloque l'accès immédiatement, réversible
3. **Faire sonner à distance** si l'appareil est probablement à proximité (dans les locaux, dans une voiture)
4. **Si non localisé après un délai raisonnable** (à définir selon la politique de l'entreprise, souvent 24-48h) : escalader vers un effacement à distance

---

## Panne 9 — Téléphone professionnel volé (vol confirmé, pas juste égaré)

### Démarche recommandée, dans l'ordre
1. **Effacement à distance immédiat** des données professionnelles (pas de délai d'attente ici, contrairement à un simple oubli)
2. **Révocation immédiate** des accès associés : comptes email, VPN, certificats liés à cet appareil
3. **Retrait de l'appareil du MDM** une fois l'effacement confirmé, pour éviter toute tentative de réenregistrement frauduleux
4. **Signalement** : selon la politique de l'entreprise, dépôt de plainte et/ou déclaration à l'assurance professionnelle

### Point important
Ne jamais attendre une "confirmation totale" du vol avant d'agir — contrairement à un oubli où la prudence invite à patienter, un vol signalé justifie une réaction immédiate, quitte à annuler certaines actions si l'appareil est finalement retrouvé (certains MDM permettent d'annuler un verrouillage, plus rarement un effacement déjà effectué).

---

## Panne 10 — Appareil personnel (BYOD) avec problème, mais l'utilisateur refuse un accès complet par crainte pour ses données privées

### Le dilemme
En BYOD (Bring Your Own Device), l'appareil appartient à l'utilisateur, qui peut légitimement refuser un contrôle total du MDM sur ses données personnelles (photos, messages privés).

### Solutions possibles
**Option A — Conteneurisation professionnelle** (si le MDM le supporte) : seule une partie "professionnelle" de l'appareil est gérée et visible par l'entreprise (applications, emails pro), le reste reste privé et invisible pour l'administrateur.

**Option B — Profil de travail Android séparé** : Android propose nativement cette séparation, gérable par le MDM sans toucher au reste de l'appareil.

**Option C — Accepter la limite** : si l'utilisateur refuse toute gestion, documenter clairement ce que l'entreprise ne peut pas garantir en termes de sécurité sur cet appareil précis.

---

## Panne 11 — L'appareil affiche "en attente" d'installation d'une application depuis des jours

**Diagnostic** :
1. Vérifier la connexion internet de l'appareil au moment prévu de l'installation
2. Vérifier si l'application nécessite le Wi-Fi uniquement (paramètre courant pour économiser les données mobiles) alors que l'appareil n'a été que sur données mobiles
3. Vérifier l'espace de stockage disponible sur l'appareil

**Vérifier dans Headwind MDM** : l'historique de tentatives d'installation par appareil montre généralement un message d'erreur précis (espace insuffisant, réseau indisponible, application incompatible avec la version d'OS).

---

## Panne 12 — Nouvel employé, appareil à configurer rapidement avant son arrivée

### Le dilemme
Le temps de préparation est souvent très court, et il faut équilibrer rapidité de mise à disposition et respect des politiques de sécurité standard.

### Démarche recommandée
1. **Préparer un "profil type"** à l'avance dans le MDM (configuration, applications, restrictions déjà définies pour ce type de poste), plutôt que de tout configurer manuellement à chaque nouvel arrivant
2. **Enrôlement en configuration zero-touch** si le MDM et les appareils le supportent — l'appareil se configure automatiquement dès la première mise sous tension, sans intervention manuelle
3. Prévoir toujours un délai de marge (par exemple, préparer l'appareil la veille, pas le matin même) pour absorber un imprévu

---

## Panne 13 — Un utilisateur signale que son téléphone professionnel "espionne" ses données personnelles

### Le dilemme
Cette inquiétude, même infondée techniquement, doit être prise au sérieux — elle touche à la confiance et peut avoir des implications légales (RGPD notamment).

### Démarche recommandée
1. **Expliquer précisément ce que le MDM voit réellement** — généralement : inventaire des applications installées, position si activée, conformité aux politiques de sécurité — mais pas le contenu des messages privés ou des photos personnelles, sauf configuration explicite et légalement encadrée
2. **Vérifier la configuration réelle** appliquée à cet appareil précis, pour confirmer qu'elle correspond bien à ce qui est communiqué à l'utilisateur
3. **Documenter la politique de confidentialité** de l'entreprise concernant les appareils gérés, pour que ce type de question ait une réponse claire et écrite, pas juste orale au cas par cas
