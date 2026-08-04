# Matériel GLPI

DeskPlan peut afficher, sur la **fiche d'une personne**, le **matériel qui lui est prêté** d'après l'inventaire **GLPI** de l'établissement. L'intégration est en **lecture seule** : DeskPlan ne modifie jamais GLPI.

## Sur la fiche personne

Un onglet **Matériel** liste les équipements GLPI rattachés à la personne. S'il reste vide alors que du matériel existe, voir « Dépannage » ci-dessous.

<!-- capture: onglet Matériel sur une fiche personne -->

## Appariement personne ↔ compte GLPI

Le rapprochement se fait par **login** au format `prénom.nom`. Un écran d'**appariement** (administration) permet de vérifier et corriger les correspondances quand l'automatique échoue (homonymes, login atypique).

Endpoints concernés (lecture) : `GET /api/glpi/users`, `GET /api/glpi/matching`, `GET /api/glpi/diagnostic`, `GET /api/personnes/{id}/materiels`. La correction manuelle passe par `PUT /api/personnes/{id}/glpi-term`.

## Prérequis réseau

!!! warning "Accès réseau requis"
    Le serveur DeskPlan doit pouvoir joindre l'API GLPI (HTTPS). Si l'onglet Matériel « tourne dans le vide » ou reste vide, c'est généralement que le **flux réseau** entre la VM et le serveur GLPI n'est pas ouvert : faites **whitelister l'IP** du serveur DeskPlan côté infrastructure GLPI.

## Diagnostic

L'endpoint `GET /api/glpi/diagnostic` aide à distinguer :

- un **blocage réseau** (timeout de connexion) — à traiter côté pare-feu ;
- un **problème d'appariement** (login non trouvé) — à corriger via l'écran d'appariement.

!!! note "Bon à savoir"
    Les emails étant souvent vides dans l'annuaire, l'appariement s'appuie sur le login `prénom.nom` (accents repliés, casse ignorée). Vérifiez ce login en cas de matériel manquant pour une personne.
