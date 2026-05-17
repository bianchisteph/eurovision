# Contribuer à BAOBAPRONO

Merci de vouloir contribuer à ce projet ! 🎤

## Table des matières

- [Code de conduite](#code-de-conduite)
- [Comment contribuer](#comment-contribuer)
- [Signaler un bug](#signaler-un-bug)
- [Proposer une amélioration](#proposer-une-amélioration)
- [Soumettre un Pull Request](#soumettre-un-pull-request)
- [Conventions de code](#conventions-de-code)
- [Conventions de commit](#conventions-de-commit)
- [Environnement de développement](#environnement-de-développement)

## Code de conduite

Ce projet adhère à un [Code de Conduite](CODE_OF_CONDUCT.md). En participant, vous vous engagez à respecter ses termes.

## Comment contribuer

### Signaler un bug

1. Vérifier que le bug n'a pas déjà été signalé dans les [Issues](https://github.com/bianchisteph/eurovision/issues)
2. Ouvrir une nouvelle issue avec le template **Bug Report**
3. Inclure :
   - Description claire du problème
   - Étapes pour reproduire
   - Comportement attendu vs observé
   - Navigateur et version
   - Captures d'écran si pertinent

### Proposer une amélioration

1. Ouvrir une issue avec le template **Feature Request**
2. Décrire clairement :
   - Le besoin / cas d'usage
   - La solution proposée
   - Les alternatives envisagées

### Soumettre un Pull Request

1. **Fork** le dépôt
2. Créer une branche depuis `main` :
   ```bash
   git checkout -b feature/ma-fonctionnalite
   ```
3. Effectuer vos modifications
4. Tester dans au moins 2 navigateurs (Chrome + Firefox recommandés)
5. Committer avec un message conventionnel (voir ci-dessous)
6. Pousser votre branche :
   ```bash
   git push origin feature/ma-fonctionnalite
   ```
7. Ouvrir un **Pull Request** vers `main` avec :
   - Description des changements
   - Captures d'écran si modifications visuelles
   - Référence à l'issue liée (`Fixes #123`)

## Conventions de code

### Généralités

- L'application est contenue dans un **fichier unique** (`index.html`) — conserver cette architecture
- Indentation : **2 espaces**
- Pas de point-virgule en fin de ligne pour le CSS inline, point-virgule obligatoire en JS
- Noms de variables et fonctions en **camelCase**
- Constantes en **UPPER_SNAKE_CASE**

### HTML

- Utiliser les classes Tailwind CSS en priorité
- Sémantique HTML5 (`<header>`, `<main>`, `<section>`)
- Attributs entre guillemets doubles

### CSS

- Animations déclarées dans le bloc `<style>` en haut du fichier
- Préfixer les classes custom avec un nom descriptif (`euro-`, `card-`, `nav-`)
- Pas de framework CSS additionnel (Tailwind via CDN suffit)

### JavaScript

- Vanilla JS uniquement (pas de jQuery, React, Vue, etc.)
- Fonctions nommées (pas de classes)
- Échapper les contenus utilisateur avec la fonction `esc()`
- Toujours vérifier l'existence des éléments DOM avant manipulation

## Conventions de commit

Ce projet suit la convention [Conventional Commits](https://www.conventionalcommits.org/) :

```
<type>(<scope>): <description>

[body]

[footer]
```

### Types

| Type | Description |
|---|---|
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction de bug |
| `docs` | Documentation uniquement |
| `style` | Formatage, pas de changement de logique |
| `refactor` | Refactoring sans ajout de fonctionnalité |
| `perf` | Amélioration de performance |
| `chore` | Maintenance, configuration |

### Exemples

```
feat(prono): ajouter le drag & drop pour réordonner les pronos
fix(scoring): corriger le calcul du bonus position exacte
docs(readme): ajouter les instructions Firebase
style(ui): ajuster les couleurs du podium
```

## Environnement de développement

### Prérequis

- Un éditeur de code (VS Code recommandé)
- Un navigateur web moderne
- Un projet Firebase pour les tests

### Lancer en local

```bash
# Ouvrir directement
start index.html       # Windows
open index.html        # macOS

# Ou via un serveur local
python -m http.server 8080
npx serve .
```

### Extensions VS Code recommandées

- [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) — Rechargement automatique
- [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss) — Autocomplétion Tailwind

### Tester

Comme l'application n'a pas de suite de tests automatisés, vérifier manuellement :

1. Création d'un joueur (prénom + mot de passe)
2. Reconnexion avec le bon mot de passe
3. Refus de connexion avec un mauvais mot de passe
4. Sélection des 10 pays (pas de doublons)
5. Verrouillage des pronos (redirection vers classement)
6. Blocage global des votes par l'admin
7. Protection de l'onglet admin par mot de passe
8. Saisie des résultats admin
9. Calcul correct des scores
10. Affichage du podium et du classement
11. Responsivité mobile

---

Merci pour votre contribution ! 🎉
