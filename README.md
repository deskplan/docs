# DeskPlan — Documentation

Documentation utilisateur, administrateur et technique de **DeskPlan**, construite avec [MkDocs Material](https://squidfunk.github.io/mkdocs-material/).

📖 **En ligne : <https://deskplan.github.io/docs/>**

## Aperçu local

```bash
pip install -r requirements.txt
mkdocs serve      # http://127.0.0.1:8000
```

## Publication

Chaque `push` sur `main` déclenche le workflow [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml) qui construit le site et le publie sur GitHub Pages. Aucune action manuelle.

> La documentation de **déploiement/infrastructure** (secrets, VM) n'est volontairement **pas** publiée ici.

## Structure

- `docs/guide/` — guide utilisateur (annuaire, bureaux, personnel, Interroger…)
- `docs/admin/` — administration (droits, LDAP, 2FA, GLPI)
- `docs/technique/` — architecture, authentification, modèle de données, API
- `docs/securite/` — audit et modèle de menace
