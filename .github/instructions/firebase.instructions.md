---
description: "Use when working with Firebase Realtime Database, configuring security rules, modifying data structure, or debugging sync issues. Covers the Firebase data model and real-time listeners."
---
# Firebase — Conventions MPP

## Structure de données

```
/players/{uid}
  ├─ name: string          (prénom affiché)
  ├─ predictions: string[10] (top 10 pronostiqué, "" si vide)
  ├─ locked: boolean       (pronos verrouillés, irréversible)
  └─ passwordHash: string  (SHA-256 du mot de passe joueur)

/realResults: string[10]   (top 10 officiel Eurovision)
/votesLocked: boolean      (blocage global des votes par l'admin)
/allowUnlock: boolean      (autorisation de déverrouillage des pronos par l'admin)
```

## Clé joueur (uid)

Générée par `toKey(prénom)` : NFD normalize → lowercase → remplacer non-alphanum par `_` → déduplication `_`.

## Listeners

- `.info/connected` → met à jour l'indicateur de connexion
- `players` → met à jour `allPlayers` → `render()`
- `realResults` → met à jour `realResults` → `render()`
- `votesLocked` → met à jour `votesLocked` → `render()`
- `allowUnlock` → met à jour `allowUnlock` → `render()`

## Scoring

Points par position réelle : {1:12, 2:10, 3:8, 4:7, 5:6, 6:5, 7:4, 8:3, 9:2, 10:1}
Bonus : si position prédite === position réelle → points doublés (×2)

## SDK

Firebase compat v10.12.0 via CDN — ne pas migrer vers le SDK modulaire.
