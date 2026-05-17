# Project Guidelines — MPP (Mon Petit Prono)

## Overview

Application web single-page de pronostics Eurovision 2026. Fichier unique `index.html` (HTML + CSS + JS), synchronisation temps réel via Firebase Realtime Database. Pas de build, pas de bundler, pas de npm.

## Architecture

- **Fichier unique** : tout le code est dans `index.html` — ne pas splitter en fichiers séparés sauf demande explicite
- **Vanilla JS** uniquement — pas de framework (React, Vue, etc.), pas de jQuery
- **Tailwind CSS via CDN** — pas d'installation locale, pas de `tailwind.config.js` fichier
- **Firebase Realtime Database via CDN** — SDK compat v10.12.0

Voir [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) pour les détails techniques.

## Code Style

### JavaScript
- Fonctions nommées, pas de classes
- Variables/fonctions en `camelCase`, constantes en `UPPER_SNAKE_CASE`
- Point-virgule obligatoire en fin d'instruction
- Toujours échapper les contenus utilisateur avec `esc()` avant insertion DOM
- Pas de `innerHTML` avec des données non échappées

### HTML
- Classes Tailwind CSS en priorité
- Sémantique HTML5 (`<header>`, `<main>`, `<section>`)
- Attributs entre guillemets doubles
- Langue : `lang="fr"`

### CSS
- Animations dans le bloc `<style>` en haut du fichier
- Préfixes custom : `euro-`, `card-`, `nav-`, `sync-`, `lock-`
- Couleurs du thème : rose `#e91e8c`, violet `#7b2ff7`, bleu `#1e3a8a`, or `#fbbf24`, dark `#0f0b1e`

## Conventions

- Interface en **français**
- Commits en anglais, format [Conventional Commits](https://www.conventionalcommits.org/)
- Données Firebase : `/players/{uid}` (name, predictions[10], locked), `/realResults[10]`, `/votesLocked`, `/allowUnlock`
- Scoring : points de base selon position réelle + bonus de proximité dégradé (×100% exact → ×0% à distance 5+)
- Authentification : pas de mot de passe joueur (sélection du prénom dans la liste), mot de passe admin en constante `ADMIN_PASSWORD`
- Récupération live : scraping eurovisionworld.com via proxy CORS (allorigins.win), mapping noms EN→FR (`COUNTRY_EN_TO_FR`), auto-refresh 60s optionnel

## Sécurité

- Toujours utiliser `esc()` pour les entrées utilisateur
- Ne jamais stocker de secrets côté client
- L'API Key Firebase est publique par design, la sécurité repose sur les règles Firebase

## Documentation

- [README.md](README.md) — présentation et installation
- [CONTRIBUTING.md](CONTRIBUTING.md) — guide de contribution
- [SECURITY.md](SECURITY.md) — politique de sécurité
- [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) — architecture technique
