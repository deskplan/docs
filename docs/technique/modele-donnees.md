# Modèle de données

Base **PostgreSQL**. Les tables métier portent une colonne **`labo`** (`cpht` / `cmls`) qui assure le cloisonnement multi-laboratoire (voir [Architecture](architecture.md)).

> Référence détaillée des champs métier : `MODELE_DONNEES.md` à la racine du dépôt.

## Entités principales

```
                 ┌───────────┐        ┌───────────┐
   référentiels  │ statut    │        │ groupe    │
   (statut,      │ fonction  │◀──────▶│           │
   fonction,     └───────────┘        └───────────┘
   groupe,             ▲                    ▲
   nationalité)        │                    │
                 ┌─────┴──────────┐         │
                 │   personne     │─────────┘
                 │ (labo)         │
                 └─────┬──────────┘
                       │  occupation (période)
                       ▼
                 ┌────────────────┐
                 │     salle       │
                 │ (labo, plan,    │
                 │  capacité)      │
                 └────────────────┘
```

### `personne`
Fiche d'une personne. Champs clés : `id`, `nom`, `prénom`, `email`, `statut_id`, `fonction_id`, `groupe_id`, `date_debut`, `date_fin`, `est_permanent`, `archive`, **`labo`**. Reliée aux référentiels et, via `occupation`, à une salle.

### `salle`
Bureau / pièce. Champs clés : `id`, `batiment`, `etage`, `numero`, `type`, `capacite`, `capacite_reelle` (constatée — prime sur `capacite`), `plan_code`, `pos_x/pos_y/pos_w/pos_h` (position sur le plan), `telephone`, `service` (occupant hors labo), **`labo`**.

### `occupation`
Rattache une personne à une salle sur une période (`date_debut`, `date_fin`). Une salle a plusieurs occupants (dans la limite de sa capacité) ; le nombre d'occupants est calculé pour l'affichage.

### Référentiels
`statut`, `fonction`, `groupe`, `nationalite` — listes de valeurs partagées, référencées par `personne`.

### `tache`
Tâches / actions liées à une personne (suivi onboarding).

## Authentification & droits

### `user_grant`
Droits applicatifs, **propres à DeskPlan**. Clé primaire `(username, labo)`, colonne `role` (`consultation` / `viewer` / `editor` / `admin`). `username` en minuscules.

### `local_user`
Comptes locaux. `username` (PK), `password_hash` (bcrypt ; **vide** = compte issu du LDAP, sans mot de passe local), `email`, `display_name`, `superadmin`. La présence d'un hash non vide distingue un compte local d'une fiche LDAP.

### `ldap_config`
Configuration LDAP (une seule ligne). `enabled`, `server_uri`, `base_dn`, `bind_dn`, `bind_password`, `user_filter`, attributs (`uid_attr`, `mail_attr`, `name_attr`, `labo_attr`), `skip_tls_verify`. Éditée par le super-admin.

### `webauthn_credential`
Clés de sécurité enrôlées (2FA). Liées à `username` ; contiennent l'identifiant de credential et le compteur anti-clonage.

### `email_otp`
Préférence de 2FA par email par utilisateur (`username`, `email`, `enabled`).

## Intégration

### `glpi_mapping`
Correspondance personne ↔ terme/utilisateur GLPI, pour l'appariement du [matériel GLPI](../admin/glpi.md) (par login `prénom.nom`).

## Vues de cloisonnement

Le schéma **`albert_scoped`** contient des vues `personne` / `salle` filtrées par le laboratoire actif (GUC `app.labo`). Les requêtes de la section [Interroger](../guide/interroger.md) s'exécutent avec `search_path = albert_scoped, public` → l'IA ne peut lire que le laboratoire courant.

!!! note "Migrations"
    Le schéma est créé/mis à jour de façon **idempotente** au démarrage du serveur (fonctions `ensure…Table` dans `internal/store`). Pas d'outil de migration externe.
