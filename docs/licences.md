# Licences

## TIE

Le logiciel TIE (moteur et wrapper) est **propriétaire**, tous droits réservés. Il est livré sous forme d'images Docker, sans son code source, et son utilisation est concédée par un **contrat de licence écrit**, qui couvre ensemble le moteur et le wrapper : bénéficiaire, nombre d'instances, durée, support. Les droits que la loi garantit à l'utilisateur légitime (articles L122-6-1 et suivants du Code de la propriété intellectuelle) sont préservés.

## Composants tiers

Les images de TIE contiennent des composants tiers, chacun sous sa propre licence. Leurs mentions sont livrées dans chaque image, dans `/licenses` :

```bash
docker run --rm --entrypoint cat <image> /licenses/THIRD_PARTY_NOTICES.txt
```

Ce fichier liste chaque bibliothèque avec sa version, sa licence et ses textes de licence, les modèles embarqués, et les paquets du système de base, dont les sources sont fournies sur demande.

Chaque image est contrôlée à sa construction : une bibliothèque sous licence incompatible avec une livraison sans sources (GPL, AGPL, SSPL, ou licence introuvable) fait échouer la construction. Les licences acceptées sont les licences permissives (MIT, BSD, Apache 2.0, ISC, PSF…) ; MPL 2.0 et LGPL sont tolérées pour des bibliothèques non modifiées.

| Modèle | Usage | Licence |
|---|---|---|
| BAAI/bge-small-en-v1.5 | Embeddings par défaut, embarqué dans l'image | MIT |
| OrdalieTech/Solon-embeddings-large-0.1 | Embeddings en français (facultatif) | MIT |
| BAAI/bge-reranker-base | Reclassement (facultatif, désactivé par défaut) | MIT |

Le modèle de langage (Mistral, Anthropic ou modèle local) n'est pas fourni : il relève de votre contrat avec son fournisseur.

## Cette documentation

Cette documentation est publiée sous licence [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/deed.fr) : vous pouvez la copier, la reprendre et l'adapter, y compris dans un cahier des charges, en citant la source.
