# Authentification

## Principe

L'**identité** provient de l'annuaire LDAP ou d'un **compte local** ; les **droits** sont propres à DeskPlan (table `user_grant`, clé = identifiant en minuscules). Un identifiant présent des deux côtés **fusionne** naturellement (mêmes droits, même 2FA).

## Ordre d'authentification

`POST /api/login` (`internal/api/auth.go → authenticate()`) essaie, dans l'ordre :

1. **Compte local** — vérification bcrypt (`local_user`). Si OK → jeton **HS256** signé par DeskPlan.
2. **LDAP direct** (si configuré et activé) — bind du compte de service, recherche par `uid`, puis re-bind avec le mot de passe fourni. Si OK → jeton **HS256**.
3. **Dex / LDAP** (repli, si LDAP direct désactivé) — password grant OIDC → jeton **RS256** (validé via JWKS).

En cas d'identifiants LDAP invalides : **401**. En cas d'annuaire injoignable : **502** (pas de repli silencieux).

## Jetons (JWT)

| | Comptes locaux + LDAP direct | Dex (repli) |
|---|---|---|
| Algorithme | **HS256** (secret dédié `LOCAL_TOKEN_SECRET`) | **RS256** (clé Dex, via JWKS) |
| Émetteur (`iss`) | `deskplan-local` | issuer Dex |
| Vérification | `verifyLocal` (HMAC + `aud` + `exp`) | JWKS + `aud` + `exp` |

La vérification (`auth.Verify`) **branche sur l'algorithme** de l'en-tête. Le secret HS256 est **fail-closed** : absent → les jetons locaux sont refusés (aucun repli sur un autre secret).

## Rôles et droits effectifs

Rôles, du moins au plus privilégié : `consultation` < `viewer` < `editor` < `admin`. Le **super-admin** est transverse (géré à part).

`EffectiveGrants(claims)` calcule le rôle par laboratoire :

- **super-admin** → `admin` sur tous les laboratoires ;
- sinon : rôle par défaut déduit du groupe/labo LDAP (**consultation**), **puis** surchargé par les droits explicites en base (`user_grant`).

Le **middleware** (`internal/auth/auth.go`) applique ensuite :

- `openPath` — chemins ouverts sans jeton (login, finish 2FA, `/api/public/*`, images de plans, health, SPA) ;
- `laboExempt` — endpoints globaux nécessitant un jeton mais pas de labo (`/api/me`, `/api/users`, `/api/local-users`, `/api/settings`, …) ;
- `consultationAllowed` — **liste blanche** stricte des lectures autorisées au rôle consultation (annuaire, groupes, salles, plans) ; tout le reste → **403**.

## Rattachement au laboratoire (LDAP)

`LabosFromDept()` mappe la valeur de l'attribut labo (par défaut `departmentNumber`) vers `cpht` / `cmls` ; la valeur `IDCS` donne **les deux**. À défaut, on tente d'associer `prenom.nom` à une fiche de l'annuaire interne (`LaboForLogin`).

## Provisioning au premier login

Au premier login LDAP réussi :

1. `RegisterDirectoryUser` crée la fiche locale (nom + email, **sans mot de passe**) → visible dans la page Utilisateurs, marquée **LDAP** ;
2. `EnsureGrant` attribue **consultation** sur le(s) labo(s) déduit(s) (sans écraser une escalade existante).

Un super-admin peut ensuite élargir les droits. Un backfill complète automatiquement les comptes provisionnés avant l'ajout de cette fonction.

## Double authentification (2FA)

Après le mot de passe, si l'utilisateur a un second facteur :

- **WebAuthn / FIDO2** — `beginWebAuthnLogin` renvoie un défi ; le jeton n'est délivré qu'après `/api/login/webauthn/finish`. **Fail-closed** : si la préparation du 2ᵉ facteur échoue, la connexion est refusée.
- **Email (OTP)** — code à 6 chiffres, `/api/login/email/finish`.

### Recours « clé perdue »

`/api/login/webauthn/recover` échange le défi clé contre un **code email** ; `/api/login/email/finish` (marqueur `recover`) délivre le jeton **et supprime les clés WebAuthn** de l'utilisateur. Voir [Double authentification (admin)](../admin/securite.md).

## Super-administrateur

Piloté par **données** : indicateur `local_user.superadmin` (via un resolver), en plus d'une éventuelle liste d'environnement. Un super-admin peut en créer/promouvoir d'autres. Le compte d'amorçage `admin` est créé au démarrage s'il n'existe pas (**à changer**).

!!! warning "Points de vigilance"
    - Le secret `LOCAL_TOKEN_SECRET` est **dédié** et aléatoire (jamais réutilisé d'un autre secret).
    - `IsSuperadmin` ne matche que la clé primaire de l'utilisateur (pas la partie locale d'un email) pour éviter une escalade par collision.
    - Voir l'[Audit de sécurité](../securite/audit.md).
