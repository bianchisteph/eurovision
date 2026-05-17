# Changelog

Toutes les modifications notables de ce projet sont documentées dans ce fichier.

Le format est basé sur [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/),
et ce projet adhère au [Semantic Versioning](https://semver.org/lang/fr/).

## [1.8.0] — 2026-05-17

### Ajouté

- **Guide & FAQ intégré (style MPG)** — Bouton `?` dans la barre de navigation ouvrant un panneau d'aide complet avec 5 sections en accordéon :
  - 🎮 **Comment jouer** — Tutoriel pas-à-pas en 5 étapes numérotées
  - 📊 **Barème des points** — Grille complète des points de base + table de bonus de proximité + exemple concret
  - 💡 **Astuces de pro** — 5 conseils stratégiques pour maximiser son score
  - ❓ **FAQ** — 5 questions/réponses fréquentes (verrouillage, égalités, score max, etc.)
  - 📜 **Règles du jeu** — 7 règles officielles du jeu
- Overlay plein écran avec backdrop blur, fermeture par clic extérieur ou bouton
- Animations CSS pour l'accordéon (chevron rotatif, hauteur animée)
- Verrouillage du scroll body pendant l'affichage du guide
- Fonctions JS : `openGuide()`, `closeGuide()`, `toggleGuideSection()`
- Classes CSS : `.guide-overlay`, `.guide-panel`, `.guide-section`, `.guide-header`, `.guide-chevron`, `.guide-body`, `.guide-step`, `.guide-step-num`, `.guide-points-grid`, `.guide-points-cell`, `.help-btn`

## [1.7.0] — 2026-05-17

### Modifié

- **Scoring : bonus de proximité dégradé** — Le bonus tout-ou-rien (position exacte = points doublés, sinon 0) est remplacé par un bonus progressif selon la distance entre position prédite et position réelle
  - Distance 0 (exact) : bonus 100% 🎯
  - Distance 1 : bonus 80% 🔥
  - Distance 2 : bonus 60% 🔥
  - Distance 3 : bonus 40% 🔥
  - Distance 4 : bonus 20% 🔥
  - Distance 5+ : bonus 0%
- Formule : `bonus = arrondi(points_base × max(0, 5 - distance) / 5)`
- Score maximum théorique inchangé : 116 points (10 positions exactes)
- Indicateurs visuels : 🎯 (exact, or), 🔥 (proximité, ambre), vert (base seule)

## [1.6.0] — 2026-05-17

### Corrigé

- **Récupération live des résultats** — Le proxy CORS unique (allorigins.win) était bloqué par Cloudflare sur eurovisionworld.com, rendant la récupération impossible

### Ajouté

- **Chaîne de proxies CORS** — Essai séquentiel de 3 proxies (allorigins.win, codetabs, corsproxy.org) avec détection automatique des pages Cloudflare
- **Fallback Wikipedia** — Si aucun proxy ne fonctionne pour eurovisionworld.com, la fonction tente automatiquement de parser la page Wikipedia `Eurovision_Song_Contest_2026`
- Fonction `fetchViaProxy(targetUrl)` — Requête HTTP via chaîne de proxies avec timeout 10s et rejet des challenges Cloudflare
- Fonction `parseEurovisionWorld(html)` — Parsing dédié des tables eurovisionworld.com avec matching flexible des noms de pays
- Fonction `parseWikipedia(html)` — Parsing dédié des tables Wikipedia (`.wikitable`) pour extraire pays + points
- Filtrage des points `> 25` pour éviter la confusion avec les numéros d'ordre de passage

### Modifié

- `fetchLiveResults()` refactorisée — Logique de récupération séparée en fonctions dédiées, messages de statut mis à jour pour refléter les sources multiples

## [1.5.0] — 2026-05-16

### Ajouté

- **Drag & drop** — Réorganisation des pronos par glisser-déposer (souris + tactile sur mobile)
- **Boutons ▲▼** — Flèches de déplacement rapide sur chaque ligne pour monter/descendre un pays
- **Échange automatique** — Choisir un pays déjà assigné à une autre position provoque un swap intelligent au lieu de bloquer
- **Insertion par décalage** — Le déplacement (drag ou boutons) insère le pays à la position cible et décale les autres vers le bas
- Fonction `moveProno(fromIdx, toIdx)` pour gérer le déplacement avec splice
- Variable d'état `dragSrcIdx` pour le suivi du drag en cours
- Styles CSS `.prono-row`, `.move-btn`, `.drag-over` pour le feedback visuel du drag & drop
- Hint UX sous le titre ("💡 Glisse les lignes ou utilise ▲▼ pour réordonner")

### Modifié

- Les sélecteurs de pays affichent désormais **tous les pays** (plus de filtrage des pays déjà utilisés) — les conflits sont résolus par échange automatique
- Les boutons ▲▼ et le drag & drop sont désactivés quand les pronos sont verrouillés
- Le hint UX est masqué quand les pronos sont verrouillés

## [1.4.1] — 2026-05-16

### Corrigé

- **Lisibilité du sélecteur de joueur** — Les options de la liste déroulante étaient illisibles (texte clair sur fond blanc du navigateur). Ajout d'un fond sombre (`#1e1b2e`) et d'un texte clair (`#e5e7eb`) sur les `<option>` du `<select>`.

## [1.4.0] — 2026-05-16

### Ajouté

- **Sélecteur de joueur** — Liste déroulante des joueurs existants sur l'écran de connexion
- Option **➕ Ajouter un nouveau prénom** pour créer un nouveau joueur
- Fonctions `populateLoginSelect()` et `onLoginSelectChange()` pour gérer la liste dynamique
- Indicateur 🔒 à côté des joueurs ayant verrouillé leurs pronos dans la liste

### Supprimé

- **Mot de passe joueur** — Plus de mot de passe requis pour se connecter
- Champ `passwordHash` supprimé du modèle de données joueur
- Fonction `hashPassword()` — plus utilisée
- Constante `PWD_KEY` — plus de hash stocké dans le `localStorage`

### Modifié

- L'écran de connexion affiche désormais un sélecteur déroulant au lieu de champs texte prénom + mot de passe
- La fonction `login()` ne vérifie plus de mot de passe
- La fonction `logout()` réinitialise le sélecteur de joueur

## [1.3.0] — 2026-05-16

### Ajouté

- Bouton **🌐 Récupérer le classement en direct** dans l'onglet Admin — scrape les résultats depuis eurovisionworld.com via proxy CORS (allorigins.win)
- Bouton **🔄 Auto** pour activer/désactiver le rafraîchissement automatique des résultats toutes les 60 secondes
- Mapping des noms de pays anglais → français (`COUNTRY_EN_TO_FR`) pour la récupération live
- Fonction `fetchLiveResults()` — parsing HTML, extraction des pays + points, tri Top 10
- Fonction `toggleAutoFetch()` — gestion de l'intervalle d'auto-refresh
- Variable d'état `autoFetchInterval` pour suivre le timer d'auto-refresh
- Arrêt automatique de l'auto-refresh en quittant l'onglet admin ou à la déconnexion

## [1.2.0] — 2026-05-16

### Ajouté

- Déverrouillage des pronos autorisé par l'admin — les joueurs peuvent modifier leurs pronos tant que l'admin le permet
- Bouton **🔓 Modifier mes pronos** visible pour les joueurs quand le déverrouillage est autorisé
- Bouton admin **🔓 Autoriser / 🔒 Interdire le déverrouillage** dans l'onglet Résultats
- Nœud Firebase `/allowUnlock` (booléen) pour contrôler l'autorisation de déverrouillage
- Fonction `unlockPronos()` pour remettre `locked` à `false` côté joueur
- Fonction `toggleAllowUnlock()` pour basculer l'autorisation admin

### Modifié

- Le verrouillage des pronos n'est plus irréversible : il peut être annulé si l'admin autorise le déverrouillage
- Le bouton de déverrouillage n'apparaît que si `allowUnlock` est actif ET `votesLocked` est inactif

## [1.1.0] — 2026-05-16

### Ajouté

- Configuration Firebase intégrée dans le code (plus d'écran de configuration)
- Authentification par mot de passe joueur (hash SHA-256 stocké dans Firebase)
- Modal de mot de passe admin pour protéger l'onglet Résultats
- Bouton **Bloquer/Débloquer les votes** dans l'admin
- Nœud Firebase `/votesLocked` pour le blocage global des votes
- Redirection automatique vers le classement après verrouillage des pronos
- Champ `passwordHash` dans les données joueur

### Supprimé

- Écran de configuration Firebase (remplacé par config en dur)
- Stockage de la config Firebase dans le `localStorage`

### Modifié

- L'écran de connexion demande désormais un prénom **et** un mot de passe
- Les pronos sont bloqués si le joueur a verrouillé OU si l'admin a bloqué les votes
- Le bouton "Effacer" est aussi désactivé quand les votes sont bloqués

## [1.0.0] — 2026-05-16

### Ajouté

- Écran de configuration Firebase (API Key, Project ID, Database URL)
- Écran de connexion avec saisie du prénom
- Onglet **Mes Pronos** : sélection du Top 10 parmi les 25 pays finalistes
- Système anti-doublon dans les sélecteurs de pays
- Verrouillage irréversible des pronostics
- Onglet **Classement** : classement en temps réel avec podium animé
- Détails par joueur avec breakdown des points par pays
- Onglet **Résultats** (admin) : saisie du Top 10 officiel de l'Eurovision
- Synchronisation temps réel via Firebase Realtime Database
- Indicateur de connexion (dot vert/rouge)
- Système de scoring avec bonus position exacte (🎯)
- Liste des joueurs inscrits avec statut (en cours / verrouillé)
- Animations : slide-up, fade-in, podium rise, confettis
- Thème sombre Eurovision (dégradé rose/violet)
- Interface responsive mobile-first (Tailwind CSS)
- Sauvegarde de la configuration Firebase dans le localStorage
- Sauvegarde du prénom utilisateur dans le localStorage
- Fonction d'échappement HTML (`esc()`) pour la sécurité XSS
- Système de toasts pour les notifications

[1.5.0]: https://github.com/bianchisteph/eurovision/compare/v1.4.1...v1.5.0
[1.4.1]: https://github.com/bianchisteph/eurovision/compare/v1.4.0...v1.4.1
[1.4.0]: https://github.com/bianchisteph/eurovision/compare/v1.3.0...v1.4.0
[1.3.0]: https://github.com/bianchisteph/eurovision/compare/v1.2.0...v1.3.0
[1.2.0]: https://github.com/bianchisteph/eurovision/compare/v1.1.0...v1.2.0
[1.1.0]: https://github.com/bianchisteph/eurovision/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/bianchisteph/eurovision/releases/tag/v1.0.0
