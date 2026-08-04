# Modèle de menace

## Données protégées

DeskPlan manipule des **données personnelles** (RGPD) : identités, statuts, fonctions, affectations de bureau, emails. La confidentialité entre laboratoires (**CPHT / CMLS**) est une exigence forte.

| Bien | Sensibilité |
|------|-------------|
| Fiches personnel (identité, statut, dates) | Élevée (RGPD) |
| Affectations de bureau | Moyenne |
| Comptes & droits, secrets (mdp de service LDAP, secret JWT) | Critique |
| Matériel GLPI | Faible (lecture seule) |

## Acteurs & surfaces

- **Utilisateur authentifié** (consultation → admin) — surface principale ; risque d'**élévation de privilège** ou d'**accès inter-laboratoire**.
- **Anonyme** — n'a accès qu'aux `openPath` (login, finish 2FA, `/api/public/*`, images de plans, SPA).
- **Super-admin** — peut tout administrer ; sa compromission est critique.
- **Réseau** — trafic client↔serveur (HTTPS), serveur↔LDAP (LDAPS), serveur↔SMTP (interne).

## Menaces et mesures

| Menace | Mesure |
|--------|--------|
| Vol de session / rejeu | JWT signés (HS256 dédié / RS256), `exp` court, HTTPS obligatoire. |
| Confusion d'algorithme (JWT) | Vérification **branchée sur l'algorithme** ; secret HS256 fail-closed. |
| Accès inter-laboratoire | Colonne `labo` + en-tête `X-Labo` + contrôle des droits par labo ; vues `albert_scoped` pour l'IA. |
| Élévation de privilège | Rôles hiérarchiques ; `consultationAllowed` = liste blanche ; `set-grant` réservé super-admin. |
| Escalade super-admin par collision d'email | `IsSuperadmin` ne matche que la clé primaire, pas la partie locale d'un email. |
| Bind LDAP anonyme / mot de passe vide | Mot de passe vide **refusé** ; re-bind obligatoire ; résultat ambigu (0/≥2) refusé. |
| Injection LDAP | Échappement des filtres (`EscapeFilter`). |
| Injection SQL | Requêtes **paramétrées** (pgx) ; vues scoperisées pour l'IA. |
| Prise de contrôle via « clé perdue » | Le code part vers l'email **du compte** (annuaire) ; usage unique, expiration 10 min ; canal email de confiance (`@polytechnique.edu`). |
| Fuite de secrets | Mot de passe de service LDAP **jamais renvoyé** ; secrets hors dépôt (`.env.prod`, `deskplan.env`). |
| 2FA fail-open | La préparation du 2ᵉ facteur est **fail-closed** (refus si indisponible). |
| DoS / brute force | *(voir [Audit](audit.md) pour l'état du rate-limiting)* |

## Hypothèses de confiance

- Le **relais SMTP interne** et l'**annuaire LDAP** de l'établissement sont dignes de confiance (réseau interne).
- La **base PostgreSQL** et les conteneurs tournent en **rootless** ; une évasion de conteneur ne donne pas root sur l'hôte.
- Les administrateurs système protègent les secrets (`.env.prod`, accès SSH par clé).

## Hors périmètre

- Sécurité physique des postes clients.
- Compromission de l'annuaire ou du relais SMTP de l'établissement.
- Attaques sur l'infrastructure OpenStack sous-jacente.

!!! info "Suivi"
    Les constats et correctifs concrets sont consignés dans l'[Audit de sécurité](audit.md), réévalué à chaque évolution majeure de l'authentification.
