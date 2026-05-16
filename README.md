# 🎤 MPP — Mon Petit Prono | Eurovision 2026

> Application web de pronostics entre amis pour la finale de l'Eurovision 2026.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## Sommaire

- [Aperçu](#aperçu)
- [Fonctionnalités](#fonctionnalités)
- [Démonstration](#démonstration)
- [Prérequis](#prérequis)
- [Installation](#installation)
- [Utilisation](#utilisation)
- [Architecture technique](#architecture-technique)
- [Système de scoring](#système-de-scoring)
- [Pays participants](#pays-participants)
- [Contribuer](#contribuer)
- [Licence](#licence)

## Aperçu

**MPP (Mon Petit Prono)** est une application single-page permettant à un groupe d'amis de pronostiquer le Top 10 de la finale de l'Eurovision 2026. Les pronostics sont synchronisés en temps réel via Firebase Realtime Database, et un classement automatique est calculé dès que les résultats officiels sont saisis.

## Fonctionnalités

- **Pronostics personnels** — Chaque joueur compose son Top 10 parmi les 25 pays finalistes
- **Sélection de joueur** — Chaque joueur choisit son prénom dans la liste existante ou en ajoute un nouveau
- **Verrouillage des pronos** — Possibilité de verrouiller ses choix avant le début du show
- **Déverrouillage autorisé** — L'administrateur peut autoriser les joueurs à modifier leurs pronos même après verrouillage
- **Blocage global des votes** — L'administrateur peut fermer les votes pour tous les joueurs
- **Synchronisation temps réel** — Tous les joueurs voient les mises à jour instantanément (Firebase Realtime Database)
- **Classement automatique** — Calcul immédiat des scores dès la saisie des résultats officiels
- **Administration protégée** — L'onglet résultats est protégé par un mot de passe admin
- **Récupération live des résultats** — Bouton pour scraper le classement en direct depuis eurovisionworld.com
- **Auto-refresh** — Rafraîchissement automatique des résultats toutes les 60 secondes (activable/désactivable)
- **Podium animé** — Visualisation du podium avec animations
- **Détails par joueur** — Breakdown complet des points obtenus par pays
- **Interface responsive** — Optimisée mobile-first avec Tailwind CSS
- **Thème Eurovision** — Interface sombre avec dégradé rose/violet fidèle à l'identité visuelle
- **Confettis** 🎉 — Animations festives lors du verrouillage et de la publication des résultats
- **Zéro backend custom** — Fonctionne entièrement côté client + Firebase

## Démonstration

**URL en ligne** : [https://bianchisteph.github.io/eurovision/](https://bianchisteph.github.io/eurovision/)

1. Ouvrir le lien (ou `index.html` localement)
2. Choisir son prénom dans la liste ou en ajouter un nouveau
3. Composer son Top 10 et verrouiller
4. Suivre le classement en temps réel

## Prérequis

- Un navigateur web moderne (Chrome, Firefox, Edge, Safari)
- Un projet [Firebase](https://console.firebase.google.com/) avec **Realtime Database** activée
- Aucune installation locale requise (pas de Node.js, pas de build)

## Installation

```bash
# Cloner le dépôt
git clone https://github.com/bianchisteph/eurovision.git
cd eurovision

# Ouvrir dans le navigateur
# Windows
start index.html
# macOS
open index.html
# Linux
xdg-open index.html
```

Alternativement, servir via un serveur HTTP local :

```bash
# Python 3
python -m http.server 8080

# Node.js (npx)
npx serve .
```

## Configuration Firebase (développeur)

La configuration Firebase est intégrée directement dans le code (`index.html`). Pour utiliser votre propre instance Firebase :

1. Créer un projet sur [Firebase Console](https://console.firebase.google.com/)
2. Activer **Realtime Database** (région `europe-west1`)
3. Modifier la constante `FIREBASE_CONFIG` dans `index.html` avec vos identifiants

### Règles de sécurité recommandées

```json
{
  "rules": {
    "players": {
      "$uid": {
        ".read": true,
        ".write": true
      }
    },
    "realResults": {
      ".read": true,
      ".write": true
    },
    "votesLocked": {
      ".read": true,
      ".write": true
    },
    "allowUnlock": {
      ".read": true,
      ".write": true
    }
  }
}
```

> ⚠️ Ces règles sont permissives (mode soirée entre amis). Pour un usage plus large, ajouter Firebase Authentication et restreindre les écritures.

## Utilisation

### Joueur

1. **Connexion** — Choisir son prénom dans la liste ou en ajouter un nouveau
2. **Mes Pronos** — Sélectionner un pays pour chaque position du Top 10 (1er = 12 pts, 2e = 10 pts, etc.)
3. **Verrouiller** — Cliquer sur "Valider mes pronos" une fois le Top 10 complet
4. **Modifier** — Si l'admin a autorisé le déverrouillage, un bouton "🔓 Modifier mes pronos" permet de changer ses choix
5. **Classement** — Redirigé automatiquement après verrouillage, suivre le classement en temps réel

### Administrateur

1. Cliquer sur l'onglet **⚡ Résultats** (mot de passe admin requis)
2. **Bloquer les votes** — Empêcher tous les joueurs de modifier leurs pronos
3. **Autoriser le déverrouillage** — Permettre aux joueurs de déverrouiller et modifier leurs pronos
4. **Récupérer le classement en direct** — Cliquer sur 🌐 pour scraper les résultats depuis eurovisionworld.com
5. **Auto-refresh** — Cliquer sur 🔄 Auto pour activer le rafraîchissement automatique (60s)
6. Vérifier le Top 10 récupéré et ajuster si nécessaire
7. Cliquer sur **💾 Publier les résultats** — le classement se met à jour en temps réel pour tous

## Architecture technique

```
eurovision/
├── index.html          # Application complète (HTML + CSS + JS)
├── README.md           # Ce fichier
├── CONTRIBUTING.md     # Guide de contribution
├── CHANGELOG.md        # Historique des versions
├── CODE_OF_CONDUCT.md  # Code de conduite
├── SECURITY.md         # Politique de sécurité
├── LICENSE             # Licence MIT
└── docs/
    └── ARCHITECTURE.md # Documentation technique détaillée
```

| Technologie | Rôle |
|---|---|
| HTML5 | Structure de la page |
| [Tailwind CSS](https://tailwindcss.com/) (CDN) | Styles utilitaires |
| JavaScript (Vanilla ES6+) | Logique applicative |
| [Firebase Realtime Database](https://firebase.google.com/docs/database) (CDN) | Persistance et synchronisation temps réel |

> L'application est un **fichier unique** (`index.html`) sans étape de build, bundler, ni dépendances npm.

## Système de scoring

### Points par position

| Position | Points |
|:---:|:---:|
| 1er | 12 |
| 2e | 10 |
| 3e | 8 |
| 4e | 7 |
| 5e | 6 |
| 6e | 5 |
| 7e | 4 |
| 8e | 3 |
| 9e | 2 |
| 10e | 1 |

### Calcul du score

Pour chaque pays dans le pronostic d'un joueur :

1. **Points de base** — Si le pays figure dans le vrai Top 10, le joueur gagne les points correspondant à la **position réelle** du pays
2. **Bonus position exacte** — Si le joueur a placé le pays à la bonne position, les points sont **doublés** (🎯)
3. **Score total** = somme de tous les points de base + bonus

### Exemple

| Prono joueur | Résultat réel | Points |
|---|---|---|
| 🇫🇷 France → 1er | 🇫🇷 France → 1er | 12 + 12 (bonus) = **24** 🎯 |
| 🇸🇪 Suède → 2e | 🇸🇪 Suède → 5e | 6 (position réelle 5e) |
| 🇮🇹 Italie → 3e | 🇮🇹 Italie non classée | **0** |

## Pays participants

25 pays finalistes de l'Eurovision 2026 :

🇦🇱 Albanie · 🇩🇪 Allemagne · 🇦🇺 Australie · 🇦🇹 Autriche · 🇧🇪 Belgique · 🇧🇬 Bulgarie · 🇨🇾 Chypre · 🇭🇷 Croatie · 🇩🇰 Danemark · 🇫🇮 Finlande · 🇫🇷 France · 🇬🇷 Grèce · 🇮🇱 Israël · 🇮🇹 Italie · 🇱🇹 Lituanie · 🇲🇹 Malte · 🇲🇩 Moldavie · 🇳🇴 Norvège · 🇵🇱 Pologne · 🇷🇴 Roumanie · 🇬🇧 Royaume-Uni · 🇷🇸 Serbie · 🇸🇪 Suède · 🇨🇿 Tchéquie · 🇺🇦 Ukraine

## Contribuer

Les contributions sont les bienvenues ! Consulter [CONTRIBUTING.md](CONTRIBUTING.md) pour les détails.

## Licence

Ce projet est distribué sous licence MIT. Voir [LICENSE](LICENSE) pour plus de détails.

---

Fait avec ❤️ pour l'Eurovision 2026 à Vienne 🇦🇹
