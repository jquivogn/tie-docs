# Présentation

Cette page décrit ce que TIE fait, concrètement, et ce qu'il ne fait pas. Pour le pourquoi et les cas d'usage, voir [jqc.fr/tie](https://jqc.fr/tie).

## Un exemple

Un dossier « Bureau » contient les comptes rendus de réunion d'une association. Une adhérente demande :

> Quelle cotisation a été votée au trimestre dernier ?

TIE comprend « le trimestre dernier » (du 1er juillet au 30 septembre), cherche dans les comptes rendus de cette période, et répond :

> La cotisation annuelle a été portée à 35 euros lors de la réunion du bureau du 12 juillet [1]. Le tarif réduit reste à 15 euros pour les étudiants [2].
>
> *Période : du 1er juillet au 30 septembre 2026*

Chaque renvoi ouvre l'extrait exact, avec son document, sa page et sa date :

| | Document | Extrait |
|---|---|---|
| [1] | CR bureau, 12 juillet 2026, page 2 | « Décision : la cotisation annuelle passe de 30 à 35 euros à compter du 1er septembre. » |
| [2] | CR bureau, 12 juillet 2026, page 2 | « Le tarif réduit (étudiants, demandeurs d'emploi) est maintenu à 15 euros. » |

Si la réponse n'est dans aucun document, TIE le dit, sans inventer.

*Exemple fictif.*

## Comment ça marche

1. **Vous déposez vos documents** dans des dossiers. TIE les découpe en extraits, calcule leur sens (embeddings), en extrait les thèmes et leur date.
2. **Vous posez une question** sur un ou plusieurs dossiers. TIE retrouve les extraits les plus pertinents et les fournit au modèle de langage.
3. **Le modèle rédige la réponse** à partir de ces seuls extraits, en les citant. TIE retire tout renvoi qui ne correspond pas à un extrait fourni, avant de vous montrer la réponse.

## Ce que fait TIE

**Répondre en citant ses sources.** Une question posée sur un ou plusieurs dossiers reçoit une réponse rédigée à partir des passages trouvés, avec des renvois numérotés `[1]`, `[2]` vers chaque extrait. Un renvoi pointe toujours vers un extrait réellement fourni au modèle : les numéros inventés sont retirés. Si l'information n'est pas dans les documents, la réponse le dit au lieu de l'inventer. La réponse est rédigée dans la langue de la question (français ou anglais).

**Chercher par le sens et par les mots.** La recherche combine trois canaux, fusionnés en un seul classement :

- la proximité de sens (embeddings), qui trouve un passage formulé autrement que la question ;
- la recherche plein texte, qui trouve un nom, un sigle ou un numéro exact ;
- un graphe de thèmes, extraits de chaque passage à l'indexation, qui relie les documents qui traitent du même sujet.

**Suivre une conversation.** Une question de suivi (« Et en septembre ? ») est reformulée en question autonome avant la recherche. Le moteur ne conserve aucune conversation : c'est l'application qui la garde.

**Résumer et relever.** Un document entier peut être résumé, jusqu'à une taille plafonnée par la configuration (500 extraits par défaut, plusieurs centaines de pages), avec des renvois vers ses passages. Un relevé parcourt intégralement une sélection de documents selon une consigne (« relève les décisions prises ») et indique les documents où il n'y a rien.

**Dater et filtrer par période.** Chaque document reçoit la date de ce qu'il décrit (la réunion, la décision, la signature), fournie à l'envoi ou extraite de son contenu. Une question peut être limitée à une période, donnée explicitement ou exprimée dans la question (« les décisions du trimestre dernier ») ; la période retenue est affichée avec la réponse.

## Formats acceptés

| Format | Ce qui est lu |
|---|---|
| PDF | Texte de chaque page, avec le numéro de page de chaque extrait |
| Word (`.docx`) | Paragraphes et tableaux dans l'ordre du document, en-têtes et pieds de page |
| PowerPoint (`.pptx`) | Titre, textes, tableaux et notes de chaque diapositive, avec son numéro |
| Excel (`.xlsx`), CSV | Chaque feuille comme un tableau ; en-tête de colonnes répété dans chaque extrait |
| Mail (`.eml`) | En-têtes et corps ; les pièces jointes sont listées, pas indexées |
| HTML, texte, Markdown | Texte et structure (titres, listes, tableaux) |

Les PDF numérisés sans couche texte, les anciens formats binaires (`.doc`, `.ppt`, `.xls`) et les mails Outlook `.msg` ne sont pas lus : les convertir en amont.

## Cloisonnement

Les documents sont rangés dans des espaces (les dossiers de l'application). Chaque recherche porte sur les espaces demandés et sur eux seuls, y compris les passages voisins ajoutés pour le contexte. Les droits d'accès des utilisateurs sont gérés par le wrapper, avant tout appel au moteur.

## Où vont les données

| Donnée | Où |
|---|---|
| Documents, extraits, index | Bases de données installées chez vous (PostgreSQL, Neo4j) |
| Calcul des embeddings | Chez vous par défaut : modèle embarqué dans l'image, ou service dédié (modèle français Solon). Un service en ligne (Mistral) est possible si vous le configurez |
| Questions, extraits retenus pour la réponse | Envoyés au modèle de langage configuré : Mistral, hébergé en Europe, ou un modèle installé chez vous |

Les journaux du moteur ne contiennent jamais le texte d'une question ni le contenu d'un document : seulement des identifiants, des volumes et des durées.

## Ce que le moteur ne fait pas

Le moteur n'authentifie personne et ne gère aucun droit : il est conçu pour n'être joignable que par le wrapper, sur un réseau privé. Il ne stocke pas les conversations et n'envoie aucune donnée ailleurs qu'au modèle de langage configuré (et au service d'embeddings, s'il en utilise un en ligne).
