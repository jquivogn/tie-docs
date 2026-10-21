# Intégrer le moteur

!!! warning "API pré-v1"
    Cette page décrit l'API du moteur en version 3.24. L'API sera stabilisée en **v1**, avec des routes versionnées (`/v1/...`) et des codes d'erreur stables : ce changement sera annoncé à l'avance. La référence complète (schéma OpenAPI, client de référence) sera publiée avec la v1. Pour intégrer le moteur avant, contactez-nous.

## Principes

- **HTTP et JSON**, sans authentification : le moteur doit rester sur un réseau privé, derrière votre application, qui contrôle les droits avant chaque appel.
- **Vous fournissez les identifiants.** Chaque document a un `doc_id` choisi par vous, unique au sein d'un espace (`space_id`). Le même `doc_id` dans deux espaces désigne deux documents distincts.
- **Le moteur ne garde aucun état d'application** : ni utilisateurs, ni conversations, ni fichiers d'origine. Votre application conserve les fichiers, pour pouvoir réindexer.
- **Versions sémantiques** : une version mineure n'ajoute que des champs ou routes facultatifs. Ignorez les champs que vous ne connaissez pas.

## Routes

| Méthode | Route | Rôle |
|---|---|---|
| `POST` | `/index` | Indexer un document (multipart : `doc_id`, `space_id`, `content_type`, `file`, et facultatifs `weight`, `doc_date`). Réindexer remplace |
| `PATCH` | `/index/{doc_id}?space_id=` | Corriger la date d'un document, sans le réindexer |
| `POST` | `/index/{doc_id}/move` | Déplacer un document vers un autre espace, sans le réindexer |
| `DELETE` | `/index/{doc_id}?space_id=` | Supprimer un document |
| `DELETE` | `/space/{space_id}` | Supprimer un espace entier |
| `POST` | `/query`, `/query/stream` | Question sur des espaces : réponse sourcée, en une fois ou en flux (SSE) |
| `POST` | `/summarize`, `/summarize/stream` | Résumé d'un document entier |
| `POST` | `/collect`, `/collect/stream` | Relevé guidé par une consigne sur une sélection de documents |
| `GET` | `/similar/{space_id}`, `/similar_to_query` | Espaces proches d'un espace ou d'un texte |
| `GET` | `/spaces/stats` | Nombre d'extraits par espace |
| `GET` | `/health`, `/ready`, `/metrics` | Santé, disponibilité, métriques (voir [Supervision](../exploitation/supervision.md)) |

## Indexer

L'indexation est synchrone : la réponse arrive quand le document est interrogeable.

```json
{"doc_type": "report", "chunk_count": 12, "replaced": false, "page_count": 8,
 "doc_date": "2026-06-12", "doc_date_source": "extracted", "doc_date_confidence": 0.9,
 "engine_version": "3.24.0", "timings_ms": {"extract": 120, "embed": 1900}}
```

Conservez `engine_version` avec chaque document : c'est ce qui permet de [réindexer seulement ce qui en profite](../exploitation/mises-a-jour.md) après une mise à jour. Le moteur accepte quatre documents en parallèle par défaut et met les suivants en file ; au-delà de la file, il répond **429** : réessayez plus tard.

## Interroger

```json
{"query": "Quelle cotisation a été votée au trimestre dernier ?", "space_ids": ["bureau"], "today": "2026-10-14"}
```

La réponse contient le texte (`answer`), les extraits (`sources`, avec document, page et date), les numéros réellement cités (`cited`, vide si la réponse ne s'appuie sur aucun extrait), la période appliquée (`period`), les tokens consommés et les durées.

- `history` : les tours précédents de la conversation, que votre application conserve.
- `date_from`, `date_to` : limiter la recherche à une période ; sinon, une période dite dans la question est reconnue à partir de `today`.
- `doc_ids` : limiter la recherche à certains documents.
- `search_only` : les extraits classés, sans génération ni coût de modèle de langage.

En flux (`/query/stream`), le moteur envoie d'abord les sources, puis le texte au fil de sa génération, puis un événement final `done`, ou `error` si la génération échoue en route : une réponse sans `done` est incomplète.

## Erreurs

| Code | Signification | Conduite |
|---|---|---|
| 422 | Requête invalide ou document illisible | Ne pas réessayer tel quel |
| 404 | Document absent de l'espace (résumé, relevé, correction de date, déplacement) | Resynchroniser |
| 409 | Déplacement vers un espace qui contient déjà ce `doc_id` | Supprimer l'un des deux |
| 413 | Fichier ou document trop volumineux | Ne pas réessayer |
| 429 | File d'indexation pleine | Réessayer avec un délai |
| 500 | Erreur interne, détail dans les journaux du moteur | Réessayer une fois |

Envoyez un en-tête `X-Request-ID` : le moteur le renvoie et l'inscrit dans ses journaux, pour retrouver une requête de bout en bout.
