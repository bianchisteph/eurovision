# Architecture technique — MPP

## Vue d'ensemble

MPP est une **Single Page Application (SPA)** entièrement contenue dans un fichier `index.html`. Elle ne nécessite aucun build, bundler, ni serveur backend. La persistance et la synchronisation temps réel sont assurées par Firebase Realtime Database.

```
┌─────────────────────────────────────────────────┐
│                  Navigateur                      │
│                                                  │
│  ┌────────────┐  ┌──────────┐  ┌─────────────┐  │
│  │    HTML     │  │   CSS    │  │ JavaScript  │  │
│  │  (structure)│  │(Tailwind)│  │  (logique)  │  │
│  └────────────┘  └──────────┘  └──────┬──────┘  │
│                                       │          │
│  ┌────────────────────────────────────┘          │
│  │                                               │
│  │  localStorage          Firebase SDK (CDN)     │
│  │  ┌──────────┐          ┌──────────────┐       │
│  │  │ username │          │ Realtime DB  │       │
│  │  │          │          │  listeners   │       │
│  │  └──────────┘          └──────┬───────┘       │
│  │                               │               │
└──┼───────────────────────────────┼───────────────┘
   │                               │
   │                               ▼
   │                 ┌─────────────────────────┐
   │                 │  Firebase Realtime DB    │
   │                 │                          │
   │                 │  /players/{uid}          │
   │                 │    ├─ name               │
   │                 │    ├─ predictions[10]    │
   │                 │    ├─ locked             │
   │                 │    └─ passwordHash       │
   │                 │                          │
   │                 │  /realResults[10]        │
   │                 │  /votesLocked             │
   │                 │  /allowUnlock             │
   │                 └─────────────────────────┘
   │
   └──► Persistance locale (session)
```

## Structure de l'application

### Écrans (Screens)

L'application possède 3 écrans principaux, gérés par la fonction `show()` qui toggle la visibilité :

| Écran | ID | Description |
|---|---|---|
| Connexion | `screen-login` | Sélection du prénom dans la liste ou ajout d'un nouveau joueur |
| Application | `screen-app` | Écran principal avec navigation par onglets |
| Modal admin | `admin-modal` | Saisie du mot de passe admin (overlay) |

### Onglets (Tabs)

L'écran principal contient 3 onglets, gérés par la fonction `navigate()` :

| Onglet | ID | Description |
|---|---|---|
| Mes Pronos | `tab-prono` | Sélection du Top 10 personnel |
| Classement | `tab-classement` | Podium et tableau des scores |
| Résultats | `tab-admin` | Saisie des résultats officiels |

### Flux de navigation

```
boot()
  │
  ├─ initFB() (config intégrée)
  │   ├─ Prénom en localStorage ?
  │   │   ├─ Non → screen-login
  │   │   └─ Oui → screen-app → tab-prono
  │   └─ Erreur → screen-login
  │
  └─ Listeners Firebase actifs
      ├─ .info/connected → syncUI()
      ├─ players → onData() → render()
      ├─ realResults → onData() → render()
      ├─ votesLocked → onData() → render()
      └─ allowUnlock → onData() → render()
```

## Modèle de données Firebase

### `/players/{uid}`

```json
{
  "name": "Stéphane",
  "predictions": ["France", "Suède", "Italie", "...", "..."],
  "locked": true
}
```

| Champ | Type | Description |
|---|---|---|
| `name` | `string` | Prénom affiché |
| `predictions` | `string[10]` | Tableau de 10 noms de pays (ou `""` si non rempli) |
| `locked` | `boolean` | `true` si les pronos sont verrouillés (réversible si l'admin autorise le déverrouillage via `allowUnlock`) |

**Clé `uid`** : générée par `toKey(prénom)` — normalisation NFD, lowercase, caractères non-alphanumériques remplacés par `_`.

### `/realResults`

```json
["France", "Suède", "Italie", "Allemagne", "Grèce", "Ukraine", "Norvège", "Belgique", "Pologne", "Croatie"]
```

Tableau de 10 pays ordonnés par position (index 0 = 1er, index 9 = 10e).

### `/votesLocked`

```json
true
```

Booléen contrôlé par l'admin. Quand `true`, aucun joueur ne peut modifier ou effacer ses pronos.

### `/allowUnlock`

```json
false
```

Booléen contrôlé par l'admin. Quand `true`, les joueurs ayant verrouillé leurs pronos peuvent les déverrouiller pour les modifier (sauf si `votesLocked` est actif).

## Algorithme de scoring

```
Pour chaque joueur :
  score_total = 0
  perfect_count = 0

  Pour chaque pays dans predictions[0..9] :
    Si le pays est dans realResults :
      position_réelle = index du pays dans realResults
      points_base = POINTS[position_réelle + 1]  // {1:12, 2:10, 3:8, ...}

      Si position_prédite == position_réelle :
        bonus = points_base   // Position exacte → points doublés
        perfect_count += 1
      Sinon :
        bonus = 0

      score_total += points_base + bonus
    Sinon :
      // Pays non classé dans le Top 10 réel → 0 point

Classement : tri par score_total DESC, puis perfect_count DESC
```

### Table des points

```
Position :  1er  2e  3e  4e  5e  6e  7e  8e  9e  10e
Points   :  12   10   8   7   6   5   4   3   2    1
```

### Score maximum théorique

Si un joueur prédit parfaitement les 10 positions :

$$S_{max} = 2 \times (12 + 10 + 8 + 7 + 6 + 5 + 4 + 3 + 2 + 1) = 2 \times 58 = 116$$

## Synchronisation temps réel

### Listeners Firebase

L'application utilise 4 listeners Firebase `.on("value")` :

1. **`.info/connected`** — Détecte l'état de la connexion et met à jour l'indicateur visuel (dot vert/rouge)
2. **`players`** — Écoute tous les changements sur les joueurs → déclenche `onData()` → `render()`
3. **`realResults`** — Écoute les changements sur les résultats officiels → déclenche `onData()` → `render()`
4. **`votesLocked`** — Écoute le blocage global des votes → déclenche `onData()` → `render()`
5. **`allowUnlock`** — Écoute l'autorisation de déverrouillage par l'admin → déclenche `onData()` → `render()`

### Flux de données

```
Joueur A modifie son prono
  → db.ref("players/joueur_a/predictions").set([...])
  → Firebase propage la modification
  → Listener "players" déclenché chez TOUS les clients
  → allPlayers mis à jour
  → render() recalcule l'affichage
```

## Gestion de l'état

L'état de l'application est géré par des variables globales :

| Variable | Type | Description |
|---|---|---|
| `db` | `firebase.database.Database` | Instance Firebase Database |
| `currentUser` | `string` | Clé du joueur actuel |
| `displayName` | `string` | Prénom affiché |
| `allPlayers` | `object` | Copie locale de tous les joueurs |
| `realResults` | `string[]` | Copie locale des résultats officiels |
| `isConnected` | `boolean` | État de la connexion Firebase |
| `votesLocked` | `boolean` | Blocage global des votes (admin) |
| `allowUnlock` | `boolean` | Autorisation de déverrouillage des pronos (admin) |
| `adminAuthed` | `boolean` | Accès admin déverrouillé pour la session |
| `autoFetchInterval` | `number\|null` | ID de l'intervalle d'auto-refresh des résultats (`null` si inactif) |
| `currentTab` | `string` | Onglet actif (`prono`, `classement`, `admin`) |

## Sécurité

### XSS

La fonction `esc()` échappe les contenus utilisateur en créant un nœud texte DOM :

```javascript
function esc(t) {
  const d = document.createElement("div");
  d.appendChild(document.createTextNode(t));
  return d.innerHTML;
}
```

Cette approche est sûre car elle utilise le mécanisme natif du navigateur pour échapper les caractères HTML.

### Identification

L'identification est basée sur le prénom + mot de passe. Le mot de passe est hashé côté client (SHA-256 via `crypto.subtle`) avant stockage dans Firebase. La clé joueur est dérivée du prénom via normalisation. Deux prénoms identiques (accents/casse ignorés) partagent le même compte (avec vérification du mot de passe).

### Administration

L'onglet admin est protégé par un mot de passe (`ADMIN_PASSWORD`) vérifié côté client via un modal. L'accès est maintenu pour la durée de la session (variable `adminAuthed`).

## Dépendances CDN

| Librairie | Version | URL |
|---|---|---|
| Tailwind CSS | Latest | `https://cdn.tailwindcss.com` |
| Firebase App | 10.12.0 | `https://www.gstatic.com/firebasejs/10.12.0/firebase-app-compat.js` |
| Firebase RTDB | 10.12.0 | `https://www.gstatic.com/firebasejs/10.12.0/firebase-database-compat.js` |

## Animations CSS

| Animation | Usage | Durée |
|---|---|---|
| `gradient-shift` | Fond de page animé | 15s en boucle |
| `slideUp` | Apparition des éléments | 0.45s |
| `fadeIn` | Fondu d'apparition | 0.3s |
| `pulse-dot` | Indicateur de connexion | 2s en boucle |
| `podiumRise` | Barres du podium | 0.6s |
| `confetti` | Confettis festifs | 2-4s (aléatoire) |

## Récupération live des résultats

L'onglet admin intègre un système de récupération automatique des résultats depuis [eurovisionworld.com](https://eurovisionworld.com/eurovision/2026) :

### Flux de récupération

```
fetchLiveResults()
  │
  ├─ Requête via proxy CORS (api.allorigins.win)
  │   └─ GET https://eurovisionworld.com/eurovision/2026
  │
  ├─ Parsing HTML (DOMParser)
  │   └─ Extraction des pays + points depuis les tables
  │
  ├─ Tri par points décroissants → Top 10
  │
  ├─ Mapping noms anglais → français (COUNTRY_EN_TO_FR)
  │   └─ ex: "Germany" → "Allemagne"
  │
  └─ Remplissage des selects admin (realResults)
      └─ L'admin vérifie et publie manuellement
```

### Auto-refresh

- Activé/désactivé via le bouton 🔄 Auto
- Intervalle : 60 secondes (`setInterval`)
- Arrêt automatique en quittant l'onglet admin ou à la déconnexion
- Variable d'état : `autoFetchInterval` (ID du timer ou `null`)

### Proxy CORS

Le scraping passe par `api.allorigins.win` pour contourner les restrictions CORS du navigateur. Ce service est public et gratuit.

## Limitations connues

- **Config en dur** — La configuration Firebase est intégrée dans le code source
- **Proxy CORS** — La récupération live dépend de la disponibilité du proxy allorigins.win
- **Fichier unique** — Peut devenir difficile à maintenir si le projet grossit
- **Pas de tests automatisés** — Tests manuels uniquement
- **CDN** — Dépendance à la disponibilité des CDN externes
- **Pas de PWA** — Pas de Service Worker, pas de mode hors-ligne
- **Admin côté client** — Le mot de passe admin protège l'interface mais pas la base de données directement
