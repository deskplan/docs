# Connexion & rôles

## Se connecter

Rendez-vous sur `https://deskplan.idcs.polytechnique.fr` (ou ouvrez l'application macOS) et saisissez votre **identifiant** et votre **mot de passe**.

DeskPlan reconnaît deux types de comptes, de façon transparente :

- **Comptes de l'annuaire (LDAP)** — vous vous connectez avec vos identifiants **Polytechnique** habituels ;
- **Comptes locaux** — créés directement dans DeskPlan par un super-administrateur (utile pour un compte de service ou une personne hors annuaire).

Un même identifiant peut exister des deux côtés : les droits et la double authentification sont alors **partagés**.

Au premier login via l'annuaire, votre fiche est créée automatiquement et vous obtenez par défaut un accès **Consultation** sur votre laboratoire. Un administrateur peut ensuite **élargir vos droits**.

## Double authentification (2FA)

Pour renforcer la sécurité, votre compte peut exiger un **second facteur** après le mot de passe :

- **Clé de sécurité** (FIDO2 / passkey physique) — vous l'insérez et la touchez ;
- **Code par email** — un code à 6 chiffres envoyé à votre adresse `@polytechnique.edu`.

### J'ai perdu ma clé de sécurité

Sur l'écran qui demande la clé, cliquez sur **« Je n'ai pas ma clé »**. Après confirmation, un **code de connexion** est envoyé à votre adresse email. Saisissez-le pour vous connecter.

!!! warning "Votre ancienne clé est retirée"
    Après une connexion par ce recours, la clé perdue est **supprimée** de votre compte. Pensez à en **enregistrer une nouvelle** depuis l'écran **Sécurité** une fois connecté.

Si vous n'avez pas d'adresse email connue, demandez à un administrateur de **réinitialiser votre 2FA** (voir [Double authentification](../admin/securite.md)).

## Les rôles

Les droits sont attribués **par laboratoire**. Une même personne peut avoir des rôles différents sur CPHT et sur CMLS.

| Rôle | Annuaire | Plans bureaux | Personnel / autres | Écriture | Administration |
|------|:---:|:---:|:---:|:---:|:---:|
| **Consultation** | ✅ (lecture) | ✅ (lecture, occupants) | ❌ | ❌ | ❌ |
| **Lecture** | ✅ | ✅ | ✅ (lecture) | ❌ | ❌ |
| **Édition** | ✅ | ✅ | ✅ | ✅ | ❌ |
| **Admin** | ✅ | ✅ | ✅ | ✅ | ✅ (labo) |
| **Super-admin** | — | — | — | — | ✅ (transverse : utilisateurs, LDAP) |

Le rôle **Consultation** correspond au profil « scientifique » : il ne voit **que** l'annuaire et les plans des bureaux (avec le détail des occupants), sans aucune autre option.

## Changer de laboratoire

Si vous avez accès à plusieurs laboratoires, un sélecteur **CPHT / CMLS** vous permet de basculer. Les données affichées sont **strictement cloisonnées** par laboratoire.

!!! note "Bon à savoir"
    Votre session expire après un certain temps d'inactivité : reconnectez-vous simplement. En cas d'erreur « Aucun laboratoire associé à ce compte », contactez un administrateur pour qu'il vous attribue un accès.
