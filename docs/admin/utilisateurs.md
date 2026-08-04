# Utilisateurs & droits

Réservé au **super-administrateur**. Accessible via la page **Utilisateurs**.

Cette page réunit **tous** les comptes — utilisateurs de l'annuaire (LDAP) et comptes locaux — fusionnés par identifiant, avec leurs accès par laboratoire.

## Comprendre la liste

Chaque ligne = une personne, avec un **avatar coloré**, son nom, son identifiant et des pastilles :

- **LDAP** (vert) — compte issu de l'annuaire, sans mot de passe local.
- **compte local** — identifiant + mot de passe gérés par DeskPlan.
- **super-admin** (orange) — droits d'administration transverses.

Les **pastilles de labo** (CPHT bleu, CMLS violet) affichent le rôle sur chaque laboratoire.

Une **barre de recherche** filtre par nom, identifiant, email, laboratoire ou rôle.

!!! info "Provisioning automatique"
    Quand une personne se connecte pour la **première fois** via l'annuaire, sa fiche est créée automatiquement (nom + email, **sans mot de passe**) avec un accès **Consultation** sur son laboratoire. Elle apparaît alors ici, prête à être promue. Les comptes déjà connectés avant cette fonction sont complétés automatiquement à l'ouverture de la page.

## Attribuer un accès à un laboratoire

Dans **« Attribuer un accès »** :

1. Saisissez l'**identifiant** (ex. `prenom.nom`).
2. Choisissez le **laboratoire** (CPHT / CMLS).
3. Choisissez le **rôle** (Consultation, Lecture, Édition, Admin).
4. Cliquez **Attribuer**.

Pour donner **plusieurs laboratoires** à une même personne, répétez l'opération. Pour **retirer** un accès, cliquez la croix sur la pastille du labo.

!!! tip "Exemple"
    Donner un accès en **Lecture** au CMLS à quelqu'un du CPHT : saisissez son identifiant → CMLS → Lecture → Attribuer.

## Escalader / réduire les droits

Le rôle attribué **prime** sur le rôle par défaut. Retirer un accès explicite fait retomber la personne sur son accès par défaut (Consultation sur son labo d'origine, tel que déduit de l'annuaire).

## Créer un compte local

Bouton **« Nouveau compte local »** : identifiant + mot de passe gérés par DeskPlan (utile pour un compte de service ou une personne hors annuaire). Vous pouvez lui donner un accès initial et le marquer super-admin. Si l'identifiant existe aussi dans l'annuaire, les deux fusionnent.

## Actions par utilisateur

| Action | Effet |
|--------|-------|
| **Définir / changer le mot de passe** | Donne (ou change) un mot de passe local. Un compte LDAP peut ainsi recevoir un login local. |
| **Réinitialiser 2FA** | Supprime les clés de sécurité **et** la 2FA email de la personne. Utilisée quand quelqu'un a **perdu sa clé** : il se reconnecte au mot de passe seul puis ré-enregistre une clé. |
| **Promouvoir / retirer super-admin** | Bascule les droits d'administration transverses (comptes locaux uniquement). |
| **Supprimer** | Retire le compte local et tous ses accès. |

Toutes les actions sensibles (création, suppression, retrait d'accès, réinitialisation 2FA, promotion) demandent une **confirmation**.

!!! warning "Clé perdue"
    Deux recours existent : l'utilisateur peut s'en sortir seul via **« Je n'ai pas ma clé »** au login (code par email), **ou** un admin clique **« Réinitialiser 2FA »** ici. Voir [Double authentification](securite.md).
