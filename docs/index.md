# Documentation TIE

TIE (Thematic Indexing Engine) est un assistant IA pour les documents d'une organisation, **installé chez vous** : il indexe vos documents et répond aux questions en citant ses sources, extrait par extrait. Cette documentation montre comment il fonctionne, comment il s'installe et comment l'intégrer. Pour découvrir l'offre et demander une démonstration sur vos propres documents : [jqc.fr/tie](https://jqc.fr/tie).

!!! info "Bêta, accès sur demande"
    TIE est en bêta et en pilote dans une première organisation. L'accès se fait sur demande, après une démonstration.

TIE se compose de deux briques :

- **l'application**, que vos équipes utilisent : comptes, dossiers, droits d'accès, questions, résumés, journal d'audit ;
- **le moteur**, un service sans interface qui indexe les documents et retrouve les passages pertinents. L'application l'appelle ; un intégrateur peut aussi l'appeler directement.

## Par où commencer

| Vous êtes | Lisez |
|---|---|
| Une organisation qui évalue TIE | [Présentation](presentation.md), puis [Licences](licences.md) ; pour une démonstration, [jqc.fr/tie](https://jqc.fr/tie) |
| Responsable de l'exploitation | [Déploiement](exploitation/deploiement.md), [Supervision](exploitation/supervision.md), [Mises à jour](exploitation/mises-a-jour.md) |
| Intégrateur | [Intégrer le moteur](integration/moteur.md) |

!!! note "Version"
    Cette documentation décrit le moteur en version **3.24**. L'API du moteur sera stabilisée en **v1** : d'ici là, la page d'intégration en donne les principes, et la référence complète sera publiée avec la v1.
