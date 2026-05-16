# Changelog

Toutes les modifications notables de ce projet sont documentées dans ce fichier.

Le format est basé sur [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/),
et ce projet adhère au [Semantic Versioning](https://semver.org/lang/fr/).

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

[1.1.0]: https://github.com/bianchisteph/eurovision/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/bianchisteph/eurovision/releases/tag/v1.0.0
