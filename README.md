# Documentation TIE

Documentation publique de TIE, pour les organisations qui évaluent la solution et les intégrateurs qui branchent son moteur. Site : https://jquivogn.github.io/tie-docs/

Le site est lié depuis la page TIE du portfolio : garder son adresse stable (ne pas renommer le dépôt ni changer `site_url`) et son contenu présentable pour un visiteur qui découvre TIE.

Le logiciel TIE est sous licence propriétaire et son code n'est pas public. Cette documentation est sous licence [CC BY 4.0](LICENSE) : vous pouvez la reprendre et l'adapter, en citant la source.

## Construire le site en local

```bash
python3 -m venv .venv && .venv/bin/pip install -r requirements.txt
.venv/bin/mkdocs serve
```

Le site est publié par GitHub Actions (`.github/workflows/pages.yml`) à chaque push sur `master`.
