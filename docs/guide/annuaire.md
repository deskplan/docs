# L'annuaire

L'**annuaire** est la vue la plus simple de DeskPlan : la liste des personnes présentes dans le laboratoire, avec leur **bureau** et leur **groupe de recherche**. C'est la seule donnée accessible à un compte de consultation, et le point d'entrée le plus rapide pour retrouver quelqu'un.

<!-- capture: vue générale de la section Annuaire (recherche + liste groupée) -->

## Ce que contient l'annuaire

Pour chaque personne, l'annuaire affiche uniquement :

- le **prénom et le nom** ;
- le **bureau** occupé aujourd'hui (numéro de salle), ou un tiret s'il n'y en a pas ;
- le **groupe de recherche** de rattachement.

Aucune donnée personnelle sensible n'y figure (ni date de naissance, ni nationalité, ni statut, ni commentaires). Ces informations ne sont visibles que dans la section **Personnel**, réservée aux rôles de lecture et d'édition.

## Rechercher une personne

1. Cliquez dans le champ **« Rechercher un nom, un bureau, un groupe… »** en haut de la liste.
2. Saisissez tout ou partie d'un nom, d'un prénom, d'un numéro de bureau ou d'un nom de groupe.
3. La liste se filtre instantanément ; le nombre de résultats est affiché au-dessus.

La recherche est **insensible à la casse et aux accents** : `muller` retrouve « Müller », `eloise` retrouve « Éloïse ».

## Les groupes

Les personnes sont **regroupées par groupe de recherche**. Chaque groupe forme une section avec son intitulé et le nombre de personnes qu'il contient. Les personnes sans groupe renseigné sont rassemblées en fin de liste sous **« Sans groupe »**.

<!-- capture: une section de groupe repliée avec le compteur de personnes -->

## La fiche d'une personne

Dans l'annuaire, chaque ligne se limite au **nom, bureau et groupe** : c'est une vue de type répertoire, sans fiche détaillée.

Pour consulter la **fiche complète** d'une personne (identité, statut, contrat, quotités, suivi, matériel…), rendez-vous dans la section **Personnel**. Cette section n'est visible que pour les rôles **Lecture**, **Édition** et **Admin**.

## Ce que voit un consultant vs un gestionnaire

| | Consultation (scientifique) | Gestionnaire (lecture / édition) |
|---|---|---|
| Annuaire (nom, bureau, groupe) | Oui | Oui |
| Plans des bureaux | Oui (lecture) | Oui |
| Fiche détaillée d'une personne | Non | Oui (section Personnel) |
| Modification des fiches | Non | Édition uniquement |

Un compte **Consultation** correspond au profil « scientifique » : il ne voit **que** l'annuaire et les plans des bureaux. Un **gestionnaire** dispose en plus de la section Personnel et de ses fiches détaillées.

!!! note "Bon à savoir"
    Le bureau affiché est celui **occupé aujourd'hui** : une occupation dont la date de fin est passée n'apparaît plus. Si un bureau semble manquant, vérifiez l'affectation dans la section **Plans des bureaux** ou la fiche de la personne dans **Personnel**.
