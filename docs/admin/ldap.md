# Annuaire LDAP

Réservé au **super-administrateur**. Section **« Annuaire LDAP »** en haut de la page **Utilisateurs**.

DeskPlan se connecte **directement** à l'annuaire de l'établissement (bind + recherche) pour authentifier les utilisateurs. **Rien n'est codé en dur** : toute la configuration se règle ici et est stockée en base.

## Configurer la connexion

| Champ | Description | Exemple |
|-------|-------------|---------|
| **Activer** | Active l'authentification LDAP. Tant que c'est décoché, seuls les comptes locaux fonctionnent. | — |
| **URI du serveur** | URL du serveur LDAP (ldaps recommandé). | `ldaps://ldap-lab.polytechnique.fr:636` |
| **BaseDN** | Racine de recherche. | `dc=id,dc=polytechnique,dc=edu` |
| **Filtre de recherche** | Filtre LDAP restreignant les comptes autorisés. | `(&(objectClass=inetOrgPerson)(\|(departmentNumber=…CPHT)(…CMLS)))` |
| **DN du compte de service** | Compte de bind (lecture de l'annuaire). | `uid=anx-phymath,ou=comptes generiques,…` |
| **Mot de passe du compte** | Mot de passe du compte de service (jamais réaffiché). | `••••••` |
| **Attributs** | Attributs à lire : identifiant (`uid`), email (`mail`), nom affiché (`cn`), attribut labo (`departmentNumber`). | — |
| **Ne pas vérifier le certificat TLS** | À n'utiliser qu'en test. | décoché |

## Tester avant d'activer

Le bouton **« Tester la connexion »** vérifie la connexion et le bind du compte de service **sans rien enregistrer**. Procédez ainsi :

1. Remplissez les champs.
2. Cliquez **Tester** — attendez le message vert « Connexion et bind réussis ».
3. Cochez **Activer**, puis **Enregistrer**.

!!! danger "Effet de l'activation"
    Dès que le LDAP est activé, **toutes** les connexions non-locales passent par le **vrai annuaire**. Les comptes de démonstration internes cessent de fonctionner ; les vraies personnes se connectent avec leurs identifiants de l'établissement. Les comptes **locaux** (ex. `admin`) continuent de fonctionner.

## Rattachement au laboratoire

Le laboratoire d'une personne est déduit de l'**attribut labo** (par défaut `departmentNumber`) :

- la valeur contient `CPHT` → laboratoire **CPHT** ;
- la valeur contient `CMLS` → laboratoire **CMLS** ;
- la valeur contient `IDCS` → **CPHT et CMLS** (les deux).

À défaut d'information exploitable dans l'attribut, DeskPlan tente d'associer l'identifiant (`prenom.nom`) à une fiche de l'annuaire interne pour en déduire le laboratoire.

!!! note "Bon à savoir"
    Le mot de passe du compte de service est stocké en base et **jamais renvoyé** à l'interface (un indicateur signale seulement qu'il est renseigné). Laissez le champ vide lors d'une modification pour le **conserver**. Pour une bonne délivrabilité des emails, pensez au SPF (voir [Déploiement], réservé aux admins système).
