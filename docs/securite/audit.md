# Audit de sécurité

Dernier audit : **2026-07-24** — revue du backend Go (authentification, 2FA, LDAP, accès aux données, requêtes IA). Chaque constat a été tracé dans le code avant d'être retenu. Voir aussi le [Modèle de menace](modele-menace.md).

## Synthèse

| # | Sévérité | Constat | État |
|---|----------|---------|------|
| 1 | 🔴 Critique | Exfiltration de secrets via l'IA `/api/query` (pas de liste blanche de tables) | ✅ **Corrigé** |
| 2 | 🟠 Élevée | Compte `admin` / `admin` par défaut | ⚠️ **Atténué** (à changer par l'exploitant) |
| 3 | 🟡 Moyenne | `GET /api/salles/{id}` non cloisonné par labo (IDOR) | ✅ **Corrigé** |
| 4 | 🟡 Moyenne | `/api/salles/disponibilites` & `/api/groupes` inter-labo | ✅ **Corrigé** |
| 5 | 🟡 Moyenne | Collision par partie locale d'email (droits / super-admin) | ✅ **Corrigé** |
| 6 | 🟡 Moyenne | 2FA clé silencieusement sautée si RP non configuré | ✅ **Corrigé** (fail-closed) |
| 7 | 🟡 Moyenne | `device-login` : code choisi par le client, sans limite | 🕒 **Suivi** |
| 8 | 🔵 Faible | SMTP : repli en clair pour les codes 2FA | 🕒 **Suivi** |
| 9 | 🔵 Faible | LDAP `InsecureSkipVerify` (option superadmin) | ℹ️ Documenté |
| 10 | 🔵 Faible | Rate-limiter sur `RemoteAddr` (derrière proxy) | 🕒 **Suivi** |
| 11 | 🔵 Faible | Pas de longueur minimale sur `LOCAL_TOKEN_SECRET` | ✅ **Corrigé** |
| 12 | 🔵 Faible | `/materiels` : contrôle labo sauté si mapping GLPI | ✅ **Corrigé** |

## Détails des corrections

### 1 — Exfiltration via l'IA (`/api/query`) 🔴 → ✅
**Constat.** L'IA (Albert) génère un `SELECT` exécuté en lecture seule sur des vues labo-scopées. Mais le validateur n'imposait **aucune liste blanche de tables** : seules `personne/salle/occupation/groupe/personnes_presentes` ont une vue scopée ; les autres tables retombaient sur `public.*`. Un utilisateur **éditeur** pouvait demander « donne le contenu de `ldap_config` / `local_user` » et récupérer le **mot de passe de service LDAP en clair** et **tous les hachages de mots de passe**.

**Correctif.** `internal/sqlvalidator` impose désormais une **liste blanche stricte** des tables autorisées (vues scopées + référentiels `ref_*`, `pays`, `labo`) ; toute référence à une autre table (`FROM`/`JOIN`) est **refusée**. Couvert par un test (`validator_test.go`).

**Renforcement recommandé (defense-in-depth).** Exécuter `/api/query` sous un **rôle PostgreSQL dédié** en lecture seule sans droit sur les tables d'auth, et **chiffrer** `ldap_config.bind_password` au repos.

### 2 — `admin` / `admin` 🟠 → ⚠️
Le compte d'amorçage `admin`/`admin` (super-admin) est joignable par login. Le serveur logue désormais un **avertissement rouge récurrent** tant que le mot de passe par défaut est actif. **Action exploitant : changer ce mot de passe immédiatement** (page Utilisateurs).

### 3–4 — Cloisonnement des salles/groupes 🟡 → ✅
`GetSalle`, `SallesDisponibilites` et `Groupes` prennent et appliquent désormais le **laboratoire actif** (filtre SQL). Un utilisateur d'un labo ne peut plus lire ni énumérer les salles/groupes d'un autre labo.

### 5 — Collision par email 🟡 → ✅
La **partie locale de l'email** (`local@…` → `local`) a été retirée de `candidateKeys` **et** du contrôle super-admin par configuration. Seuls des identifiants **stables et complets** sont désormais comparés → plus d'héritage de droits ni de promotion super-admin par collision.

### 6 — 2FA fail-closed 🟡 → ✅
Si un utilisateur a une clé enregistrée mais que le service WebAuthn est indisponible (RP non configuré), la connexion est **refusée** au lieu de sauter le 2ᵉ facteur.

### 11 — Secret de jeton 🔵 → ✅
Un `LOCAL_TOKEN_SECRET` de moins de 32 octets **désactive** les comptes locaux (fail-closed) plutôt que d'accepter un secret forgeable.

### 12 — Matériel GLPI 🔵 → ✅
`GET /api/personnes/{id}/materiels` vérifie l'appartenance au labo **avant** de servir un mapping GLPI.

## Suivi (non bloquant)

- **7 — device-login** : faire générer le `code` par le **serveur** (≥128 bits) et limiter les tentatives de `/poll`.
- **8 — SMTP** : ne pas retomber en clair pour les codes 2FA ; vérifier/épingler le certificat du relais.
- **10 — rate-limiting** : lire l'IP réelle via l'en-tête du proxy de confiance, borner la table, étendre la couverture (`recover`, `email/finish`, `device-login`).

## Points vérifiés sains

- **Pas de confusion d'algorithme JWT** : HS256 (secret dédié) vs RS256 (JWKS) ; `none` refusé ; `iss`/`aud`/`exp` validés.
- **Pas d'injection SQL** : requêtes paramétrées ; le seul SQL construit interpole des identifiants constants ou le SELECT déjà validé.
- **Écritures cloisonnées** par labo (PUT/DELETE personne, salle, occupation, tâche ; import).
- **`consultationAllowed`** = liste blanche stricte de lectures (bloque les écritures).
- **Endpoints super-admin** re-vérifient le rôle dans chaque handler.
- **Secrets non divulgués** : `bind_password` jamais renvoyé ; erreurs internes journalisées mais réponses génériques.
- **OTP email** non brute-forçable : usage unique, comparaison à temps constant, lié au préauth/utilisateur.
