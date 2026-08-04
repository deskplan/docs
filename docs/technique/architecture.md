# Architecture

## Vue d'ensemble

```
        ┌──────────────┐        ┌──────────────┐
        │  App web     │        │  App macOS   │
        │ (SvelteKit)  │        │  (SwiftUI)   │
        └──────┬───────┘        └──────┬───────┘
               │  HTTPS (Bearer JWT + X-Labo)  │
               └───────────────┬───────────────┘
                               ▼
                   ┌───────────────────────┐
                   │   Caddy (reverse-proxy │
                   │   TLS Let's Encrypt)   │
                   └───────────┬───────────┘
                               ▼
                   ┌───────────────────────┐
                   │  Serveur Go (Huma v2)  │  ← sert AUSSI le build web
                   │  /api/* + SPA          │
                   └───┬───────┬────────┬───┘
                       │       │        │
             ┌─────────┘   ┌───┘    ┌───┴──────────┐
             ▼             ▼        ▼              ▼
     ┌────────────┐  ┌─────────┐ ┌──────┐  ┌──────────────┐
     │ PostgreSQL │  │  LDAP   │ │ Dex  │  │ SMTP / GLPI  │
     │            │  │(annuaire)│ │(OIDC)│  │ (externes)   │
     └────────────┘  └─────────┘ └──────┘  └──────────────┘
```

## Composants

| Composant | Technologie | Rôle |
|-----------|-------------|------|
| **Frontend web** | SvelteKit (adapter-static) + Tailwind | SPA ; buildée puis servie par le serveur Go. |
| **Client macOS** | SwiftUI (SwiftPM), natif | Mêmes fonctionnalités ; parle uniquement à l'API. |
| **API / serveur** | Go + [Huma v2](https://huma.rocks) + `net/http` | `/api/*` + service du build web + images de plans. |
| **Base de données** | PostgreSQL 15 (pgx v5) | Données métier, droits, config LDAP, comptes locaux. |
| **Annuaire** | LDAP (bind + recherche directe) | Authentification des utilisateurs de l'établissement. |
| **Dex** | OIDC | Repli d'authentification (password grant) — optionnel. |
| **Reverse-proxy** | Caddy (TLS) | HTTPS ; seul exposé publiquement. |
| **Email** | SMTP (relais interne) | 2FA / recours « clé perdue ». |
| **GLPI** | API GLPI (lecture seule) | Matériel prêté, sur la fiche personne. |

## Principes structurants

- **Un seul service** sert l'API **et** l'application web (`web/build`). Le client macOS ne parle qu'à l'API.
- **Multi-tenant strict** : chaque donnée porte une colonne `labo` (`cpht` / `cmls`). L'API filtre selon le laboratoire actif (en-tête `X-Labo` + droits du jeton). Voir [Modèle de données](modele-donnees.md).
- **Auth découplée de l'identité** : l'identité vient de l'annuaire LDAP (ou d'un compte local) ; les **droits** (`user_grant`) sont propres à DeskPlan. Voir [Authentification](authentification.md).
- **Artefacts pré-compilés** : le binaire Go (linux/amd64) et le build web sont compilés en amont (Mac/CI) puis copiés dans une image runtime — le build sur la VM reste rapide et fiable.

## Organisation du dépôt

```
server-go/           Serveur Go
  internal/api/      Handlers HTTP (Huma) : auth, personnes, salles, query, users…
  internal/auth/     JWT, middleware, rôles, super-admin
  internal/store/    Accès PostgreSQL (pgx)
  internal/ldap/     Authentification LDAP directe
  internal/mail/     Envoi SMTP
  internal/glpi/     Client GLPI (lecture)
web/                 Frontend SvelteKit
  src/lib/sections/  Sections (Accueil, Personnel, Bureaux, Annuaire, Interroger, Qualité)
  src/lib/components/ Composants réutilisables + design system (app.css)
  src/routes/        Pages (login, utilisateurs, sécurité…)
MacClient/           Client SwiftUI
deploy/              Déploiement (Quadlet, Caddy, DEPLOYMENT.md) — non publié
docs/                Cette documentation (MkDocs)
```

## Flux d'une requête authentifiée

1. Le client envoie `Authorization: Bearer <JWT>` et `X-Labo: cpht|cmls`.
2. Le middleware **vérifie** le jeton (HS256 local ou RS256 Dex selon l'algorithme), charge les **claims**.
3. Il calcule les **droits effectifs** (rôle par labo) et le statut super-admin.
4. Il applique le **cloisonnement** : le laboratoire actif doit être autorisé ; le rôle doit suffire pour l'action.
5. Le handler exécute la requête, scoping SQL par `labo`.

!!! note "Pour aller plus loin"
    - [Authentification](authentification.md) — jetons, rôles, LDAP, 2FA.
    - [Modèle de données](modele-donnees.md) — entités et relations.
    - [API REST](api.md) — endpoints.
