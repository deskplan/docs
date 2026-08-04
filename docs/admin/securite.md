# Double authentification (2FA)

DeskPlan prend en charge deux seconds facteurs, en plus du mot de passe :

- **Clé de sécurité** (WebAuthn / FIDO2 — passkey physique) ;
- **Code par email** (OTP à 6 chiffres envoyé à l'adresse `@polytechnique.edu`).

La 2FA n'est exigée que pour les comptes qui ont **enrôlé une clé** ou **activé la 2FA email**. Le déploiement peut ainsi être progressif.

## Côté utilisateur

- **Enrôler une clé** : écran **Sécurité** → ajouter une clé (insérer + toucher).
- **Activer la 2FA email** : écran **Sécurité** → activer (nécessite un serveur d'envoi configuré).
- **Clé perdue** : au login, bouton **« Je n'ai pas ma clé »** → code par email → connexion → l'ancienne clé est retirée → ré-enrôler une nouvelle clé.

## Côté administrateur

### Réinitialiser la 2FA de quelqu'un

Page **Utilisateurs** → sur la personne → **« Réinitialiser 2FA »**. Cela supprime **toutes** ses clés et sa 2FA email. Elle pourra se reconnecter au mot de passe seul, puis ré-enrôler une clé.

C'est le recours quand une personne a perdu sa clé **et** n'a pas d'adresse email utilisable.

### Cas particulier : le super-admin a perdu sa clé

Si le **seul** super-administrateur perd sa clé et ne peut pas recevoir de code, un accès direct à la base est nécessaire (procédure « break-glass », réservée aux administrateurs système — voir `deploy/DEPLOYMENT.md`).

## Le recours « clé perdue » par email, en détail

1. L'utilisateur saisit son mot de passe → l'écran demande la clé.
2. Il clique **« Je n'ai pas ma clé »** → **confirmation** (« un code va être envoyé, votre clé sera retirée »).
3. Un code à 6 chiffres part vers son adresse email (valable 10 minutes, à usage unique).
4. Il saisit le code → connecté ; ses clés WebAuthn sont supprimées.
5. Il ré-enrôle une clé depuis l'écran **Sécurité**.

!!! warning "Prérequis : envoi d'email"
    Ce recours et la 2FA email exigent un serveur SMTP configuré côté serveur. Sans cela, le bouton renvoie « contactez un administrateur » et seul le recours admin (**Réinitialiser 2FA**) est possible.

!!! note "Bon à savoir"
    Un changement de domaine de l'application invalide les clés WebAuthn existantes (elles sont liées au domaine). Après une migration, les utilisateurs doivent **ré-enrôler** leur clé — le recours email facilite cette transition.
