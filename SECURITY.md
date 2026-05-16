# Politique de sécurité

## Versions supportées

| Version | Supportée |
|---|---|
| 1.2.x | ✅ |
| 1.1.x | ❌ |
| 1.0.x | ❌ |

## Signaler une vulnérabilité

Si vous découvrez une vulnérabilité de sécurité dans MPP, merci de la signaler de manière responsable.

### Procédure

1. **Ne pas** ouvrir une issue publique pour les vulnérabilités de sécurité
2. Envoyer un rapport via les [Security Advisories GitHub](https://github.com/bianchisteph/eurovision/security/advisories/new)
3. Inclure dans votre rapport :
   - Description de la vulnérabilité
   - Étapes pour reproduire
   - Impact potentiel
   - Suggestion de correction (si possible)

### Délai de réponse

- **Accusé de réception** : sous 48 heures
- **Évaluation initiale** : sous 7 jours
- **Correctif** : selon la gravité, sous 30 jours maximum

## Considérations de sécurité

### Architecture

MPP est une application **100% côté client**. Il n'y a pas de serveur backend custom. Les considérations de sécurité portent principalement sur :

### Firebase Realtime Database

- Les règles de sécurité Firebase sont configurées par l'utilisateur hébergeant l'instance
- En mode soirée entre amis (usage prévu), des règles permissives sont acceptables
- Pour un usage plus large, implémenter Firebase Authentication et restreindre les écritures :

```json
{
  "rules": {
    "players": {
      "$uid": {
        ".read": true,
        ".write": "auth !== null && auth.uid === $uid"
      }
    },
    "realResults": {
      ".read": true,
      ".write": "auth !== null && root.child('admins').child(auth.uid).exists()"
    },
    "votesLocked": {
      ".read": true,
      ".write": "auth !== null && root.child('admins').child(auth.uid).exists()"
    },
    "allowUnlock": {
      ".read": true,
      ".write": "auth !== null && root.child('admins').child(auth.uid).exists()"
    }
  }
}
```

### Protection XSS

- Tous les contenus utilisateur (prénoms) sont échappés via la fonction `esc()` avant insertion dans le DOM
- Aucun usage de `innerHTML` avec des données non échappées

### Stockage local

- Le prénom de l'utilisateur est stocké dans le `localStorage`
- Pas de mot de passe joueur — l'accès se fait par sélection du prénom (mode soirée entre amis)
- Le mot de passe admin est vérifié côté client (protection contre l'accès à l'onglet, pas contre la modification directe de la DB)
- L'API Key Firebase est une clé publique (côté client) sécurisée par les règles Firebase

### Dépendances externes

| Dépendance | Source | Risque |
|---|---|---|
| Tailwind CSS | CDN (cdn.tailwindcss.com) | Intégrité dépendante du CDN |
| Firebase SDK | CDN (gstatic.com) | Librairie officielle Google |

> Pour renforcer la sécurité, envisager d'ajouter des attributs `integrity` (SRI) aux balises `<script>` et de servir les dépendances en local.

## Bonnes pratiques pour les utilisateurs

1. Utiliser Firebase en mode **production** avec des règles de sécurité appropriées
2. Ne pas partager l'URL de la base de données publiquement si les règles sont permissives
3. Limiter le nombre de joueurs au groupe concerné
4. Supprimer la base de données après l'événement si elle n'est plus utile
