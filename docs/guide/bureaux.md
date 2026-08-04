# Plans des bureaux

La section **Bureaux** cartographie les salles du laboratoire et leur **occupation**. Elle permet de visualiser qui occupe quel bureau, de repérer les places libres, et — pour les éditeurs — d'**assigner** ou de **libérer** une personne.

<!-- capture: vue générale de la section Bureaux (plan interactif + panneau de détail) -->

## Deux façons de voir les bureaux

DeskPlan propose deux modes d'affichage :

- **Plan interactif** — le plan réel de l'étage (image), avec les salles cliquables et colorées selon leur occupation ;
- **Liste** — une grille schématique par **bâtiment** et **étage**, utile quand aucun plan n'est disponible.

### Naviguer dans les plans et bâtiments

- En mode **Plan**, sélectionnez le plan (bâtiment / étage) à afficher.
- En mode **Liste**, choisissez le **bâtiment** puis l'**étage** (`RDC`, `É1`, `É2`…).

### Zoomer et se déplacer

Sur le plan interactif :

- **Zoomer / dézoomer** : boutons **+** / **−**, ou **Ctrl + molette** (pincer sur trackpad) ;
- **Se déplacer** (pan) : cliquer-glisser, ou deux doigts sur trackpad ;
- **Ajuster** : le bouton central réinitialise le zoom et recentre le plan.

Le plan est agrandi par défaut pour que les petites salles restent lisibles.

## Lire l'occupation d'une salle

Chaque salle affiche son **nombre d'occupants sur sa capacité** (ex. `2/3`) et une couleur qui résume son état :

| État | Signification |
|---|---|
| Libre | Aucun occupant |
| Partiel | Des places restent disponibles |
| Complet | Capacité atteinte |
| Surcapacité | Plus d'occupants que la capacité |

Les occupants sont représentés par leurs initiales : **bleu marine** pour un permanent, **orange** pour un non-permanent.

!!! info "Capacité réelle"
    Quand une **capacité constatée à l'audit** est renseignée, elle prime sur la capacité théorique pour tous les calculs d'occupation et de places libres.

## Voir les occupants d'un bureau

1. Cliquez sur une salle (sur le plan ou dans la grille).
2. Le **panneau de détail** s'ouvre à droite : numéro, type de salle, bâtiment / étage, capacité et **liste des occupants**.

Pour chaque occupant sont indiqués son nom, son statut et s'il est **permanent** ou **non permanent**. Une salle rattachée à un autre service (hors laboratoire) est signalée comme telle et n'est pas attribuable.

<!-- capture: panneau de détail d'une salle avec la liste des occupants -->

## Trouver les places libres à une date

1. Cliquez sur **« Places libres… »** dans la barre d'actions.
2. Choisissez une **date**.
3. Lancez la recherche : la liste des salles ayant au moins une place libre à cette date s'affiche, groupée par bâtiment.
4. Cliquez une salle pour l'ouvrir directement dans le plan.

## Assigner ou libérer une personne (éditeurs)

Ces actions sont réservées aux rôles **Édition** et **Admin**. Un compte **Consultation** voit les plans et les occupants, mais **en lecture seule** : aucun bouton d'assignation, d'édition ou de suppression n'apparaît.

Seuls les bureaux **attribuables** (type « bureau », hors salles de service) peuvent recevoir une personne.

### Assigner depuis un bureau

1. Sélectionnez la salle.
2. Cliquez sur **« Assigner »** dans le panneau de détail.
3. Recherchez la personne (nom, prénom, statut). L'option **« Masquer les personnes déjà logées »** évite les doublons.
4. Cliquez sur la personne pour l'affecter au bureau.

Si la salle est déjà pleine, un avertissement signale que l'ajout dépassera la capacité (l'affectation reste possible).

### Placer une personne depuis sa fiche

Depuis la fiche d'une personne (section **Personnel**), le bouton **« Placer dans un bureau »** ouvre le plan en **mode placement** : il suffit alors de **cliquer un bureau libre** pour y affecter la personne.

### Libérer un bureau

Dans la liste des occupants d'une salle, cliquez sur **« Libérer »** en face de la personne concernée : son occupation prend fin et la place se libère.

### Gérer les salles

Les éditeurs peuvent aussi **créer une salle** (bouton « Nouvelle salle »), **modifier ses caractéristiques** en cliquant directement sur une valeur du panneau (type, ligne fixe, capacité) et **supprimer** une salle. La surface relevée sur plan est affichée en lecture seule.

!!! note "Bon à savoir"
    L'occupation est **datée** : assigner une personne crée une occupation qui démarre aujourd'hui par défaut. Le plan affiche toujours l'occupation **du jour** ; pour vérifier une situation à une autre date, utilisez **« Places libres… »**. Une salle en **surcapacité** est mise en évidence en rouge et remonte aussi dans la section **Qualité**.
