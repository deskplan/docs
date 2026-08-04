# Interroger (IA)

La section **Interroger** permet de poser des questions **en langage naturel** sur les données de votre laboratoire. Une IA (Albert) traduit votre question en requête et affiche le résultat sous forme de tableau.

## Poser une question

1. Saisissez votre question en français, par exemple :
   - « Combien de doctorants sont présents ? »
   - « Liste des personnes dont le contrat se termine dans les 3 mois »
   - « Bureaux du bâtiment 6 avec une place libre »
2. Le résultat s'affiche en **tableau**.
3. Vous pouvez **exporter** le résultat (Excel).

<!-- capture: écran Interroger avec une question et son tableau de résultat -->

## Comprendre le résultat

Une option permet d'**expliquer** la requête générée (ce que l'IA a compris), utile pour vérifier que la question a bien été interprétée.

## Périmètre & confidentialité

!!! danger "Cloisonnement strict"
    L'IA ne peut interroger **que** les données de votre **laboratoire actif**. Techniquement, la requête s'exécute sur des vues filtrées par laboratoire (`albert_scoped`) : quelle que soit la formulation, elle ne peut pas lire les données d'un autre laboratoire.

## Conseils

- Formulez des questions **précises** (période, statut, bâtiment).
- Si le résultat semble faux, utilisez l'**explication** pour vérifier l'interprétation, puis reformulez.

!!! note "Bon à savoir"
    L'IA lit les données mais ne les **modifie jamais** : Interroger est en lecture seule. Pour modifier, passez par [Personnel](personnel.md) ou [Bureaux](bureaux.md).
