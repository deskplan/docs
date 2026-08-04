# DeskPlan

**DeskPlan** est l'application de gestion du **personnel** et des **bureaux** des laboratoires **CPHT** et **CMLS** de l'École polytechnique (infrastructure IDCS).

Elle est disponible sous deux formes, avec les **mêmes fonctionnalités** :

- une **application web** (navigateur) — `https://deskplan.idcs.polytechnique.fr` ;
- une **application macOS** native.

---

## À quoi ça sert

<div class="grid cards" markdown>

- :material-account-group: **Annuaire**
  Rechercher une personne, voir sa fiche, son bureau, son groupe.

- :material-floor-plan: **Plans des bureaux**
  Visualiser l'occupation des bureaux sur les plans, assigner ou libérer des places.

- :material-badge-account: **Personnel**
  Gérer les fiches (arrivées, départs, statuts, fonctions, archivage).

- :material-robot: **Interroger**
  Poser des questions en langage naturel sur les données du laboratoire (IA Albert).

</div>

## Selon qui vous êtes

DeskPlan est **multi-laboratoire** (CPHT / CMLS) et **multi-rôle**. Ce que vous voyez dépend de votre rôle :

| Rôle | Ce qu'il peut faire |
|------|---------------------|
| **Consultation** (scientifique) | Lecture seule : **annuaire** + **plans des bureaux** avec les occupants. Rien d'autre. |
| **Lecture** (viewer) | Consultation de toutes les sections, sans modification. |
| **Édition** (editor) | Lecture + création/modification (personnel, bureaux…). |
| **Admin** | Édition + administration du laboratoire. |
| **Super-admin** | Gestion des utilisateurs, des droits, de la connexion LDAP (transverse). |

Voir [Connexion & rôles](guide/connexion.md) pour le détail.

## Par où commencer

- **Utilisateur** → [Guide utilisateur](guide/connexion.md)
- **Administrateur** → [Administration](admin/utilisateurs.md)
- **Développeur / intégrateur** → [Technique](technique/architecture.md)

!!! info "Déploiement"
    La procédure de mise en production (infrastructure, VM, TLS, secrets) n'est **pas** publiée ici. Elle est maintenue à part, dans le dépôt (`deploy/DEPLOYMENT.md`), réservée aux administrateurs système.
