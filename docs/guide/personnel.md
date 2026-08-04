# Personnel

La section **Personnel** gère les fiches des personnes du laboratoire : identité, statut, fonction, groupe, dates d'arrivée et de départ, bureau.

Accessible aux rôles **Lecture** (consultation) et **Édition / Admin** (modification). Le rôle **Consultation** n'y a pas accès (il utilise l'[Annuaire](annuaire.md)).

## Parcourir et rechercher

- La **liste** affiche les personnes du laboratoire actif.
- La **recherche** filtre par nom, prénom, email…
- Des **filtres** permettent de restreindre par statut, permanent / non-permanent, présence.

<!-- capture: liste du personnel avec la barre de recherche et les filtres -->

## Consulter une fiche

Cliquez sur une personne pour ouvrir sa **fiche** : identité, statut et fonction, groupe, bureau attribué, dates, et — si disponible — le [matériel GLPI](../admin/glpi.md) qui lui est prêté.

## Créer / modifier (Édition)

- **Nouvelle personne** : renseignez au minimum nom, prénom, statut ; complétez fonction, groupe, dates.
- **Modifier** : ouvrez la fiche puis éditez les champs.

Les dates d'**arrivée** et de **départ** alimentent le tableau de bord (arrivées / départs à venir) et le calcul des présents.

## Arrivées & départs

- Une **date de fin** future signale un départ à venir.
- Une **date de début** future signale une arrivée à venir.

Ces échéances sont reprises sur l'**Accueil** (30 jours à venir).

## Archiver / supprimer

- **Archiver** retire la personne des vues actives tout en conservant l'historique.
- **Suppression définitive** (endpoint dédié) est réservée aux cas de correction — action irréversible, à confirmer.

!!! warning "Cloisonnement par laboratoire"
    Une fiche appartient à **un** laboratoire (CPHT ou CMLS). Vous ne voyez et ne modifiez que le personnel du laboratoire actif ; changez de laboratoire via le sélecteur si vous avez plusieurs accès.

!!! note "Bon à savoir"
    L'affectation d'un bureau se fait aussi depuis les [Plans des bureaux](bureaux.md) (Assigner / Libérer). Les deux vues restent cohérentes.
