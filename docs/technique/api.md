# API REST

API HTTP servie par le serveur Go (Huma v2). Toutes les routes sont sous `/api`. Sauf mention « ouvert », un **jeton Bearer** est requis ; les endpoints métier exigent aussi l'en-tête **`X-Labo`** (`cpht` / `cmls`) et un rôle suffisant.

Conventions de rôle : **C** = consultation, **V** = viewer (lecture), **E** = editor (écriture), **A** = admin, **SA** = super-admin.

## Authentification & session

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| POST | `/api/login` | ouvert | Identifiant + mot de passe → jeton, ou défi 2FA. |
| POST | `/api/login/webauthn/finish` | ouvert | Valide la clé de sécurité → jeton. |
| POST | `/api/login/webauthn/recover` | ouvert | Clé perdue → envoie un code par email. |
| POST | `/api/login/email/finish` | ouvert | Valide le code email → jeton (retire la clé si recours). |
| GET | `/api/me` | jeton | Profil + laboratoires et rôles. |
| GET | `/api/version` | jeton | Version du service (vérif MAJ macOS). |
| GET | `/health` | ouvert | Sonde de santé. |

## Personnel

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| GET | `/api/personnes` | V | Liste (scope labo). |
| GET | `/api/personnes/{id}` | V | Fiche. |
| POST | `/api/personnes` | E | Créer. |
| PUT | `/api/personnes/{id}` | E | Modifier. |
| DELETE | `/api/personnes/{id}` | E | Archiver. |
| DELETE | `/api/personnes/{id}/definitif` | A | Suppression définitive. |
| GET | `/api/personnes/{id}/taches` · POST · PATCH `/api/taches/{id}` · DELETE | V/E | Tâches liées. |
| GET | `/api/personnes/{id}/materiels` | V | Matériel GLPI (lecture). |

## Salles & plans

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| GET | `/api/salles` | C | Salles + occupants. |
| GET | `/api/salles/{id}` | C | Détail salle. |
| GET | `/api/salles/disponibilites` | V | Disponibilités à une date. |
| POST | `/api/salles` · PUT `/api/salles/{id}` · DELETE | E | Gérer les salles. |
| GET | `/api/plans` | C | Plans / étages. |
| POST | `/api/occupations` · DELETE `/api/occupations/{id}` | E | Assigner / libérer une place. |

## Annuaire & requêtes

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| GET | `/api/annuaire` | C | Annuaire (champs limités). |
| POST | `/api/query` | V | Question en langage naturel (IA, scope labo). |
| POST | `/api/query/explain` | V | Explique la requête générée. |
| POST | `/api/query/export` · GET `/api/query/models` | V | Export résultat / modèles dispo. |
| GET | `/api/public/directory` | ouvert | Annuaire public restreint (champs limités, scope `?labo=`). |

## Import / export

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| GET | `/api/export/personnes` | V | Export Excel. |
| POST | `/api/import/personnes` | A | Import Excel. |

## Utilisateurs & droits (super-admin)

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| GET | `/api/users` | SA | Liste des droits (+ backfill identités LDAP). |
| PUT | `/api/users/{username}/grants` | SA | Attribuer un rôle sur un labo. |
| DELETE | `/api/users/{username}/grants` | SA | Retirer un accès. |
| GET · POST | `/api/local-users` | SA | Lister / créer un compte local. |
| DELETE | `/api/local-users/{username}` | SA | Supprimer. |
| PUT | `/api/local-users/{username}/superadmin` | SA | (Dé)promouvoir super-admin. |
| POST | `/api/local-users/{username}/set-password` | SA | Définir un mot de passe. |
| POST | `/api/local-users/{username}/reset-2fa` | SA | Réinitialiser la 2FA. |

## Paramètres LDAP (super-admin)

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| GET | `/api/settings/ldap` | SA | Config LDAP (mdp de service masqué). |
| PUT | `/api/settings/ldap` | SA | Enregistrer la config. |
| POST | `/api/settings/ldap/test` | SA | Tester connexion + bind. |

## 2FA & clés (self-service)

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| GET | `/api/email-otp` · POST `/enable` · `/disable` | jeton | Statut / (dé)activer la 2FA email. |
| GET | `/api/webauthn/credentials` · DELETE `/{id}` | jeton | Lister / supprimer ses clés. |
| POST | `/api/webauthn/register/begin` · `/finish` | jeton | Enrôler une clé. |

## Connexion d'appareil (macOS)

| Méthode | Chemin | Rôle | Description |
|--------|--------|:----:|-------------|
| GET | `/api/device-login/poll` | ouvert | L'app interroge jusqu'à obtenir le jeton. |
| POST | `/api/device-login/complete` | ouvert | La page web relaie le jeton (par `code`). |

!!! note "Détails"
    Le contrôle d'accès (openPath / laboExempt / consultationAllowed), la vérification des jetons et le cloisonnement sont décrits dans [Authentification](authentification.md).
