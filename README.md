# Système de recommandation de films sur MovieLens

Prédire la note qu'un utilisateur donnerait à un film qu'il n'a pas vu, à partir des 100 000 notes du jeu MovieLens 100k.

L'approche retenue n'est pas la factorisation matricielle mais un **modèle à effets, régularisé** — plus simple, plus interprétable, et étonnamment difficile à battre sur ce jeu.

## Le modèle

Une note se décompose en trois termes :

```
note prédite  =  moyenne générale
               + effet du film        (ce film plaît-il plus que la moyenne ?)
               + effet de l'utilisateur  (cet utilisateur note-t-il large ou sévère ?)
```

La **moyenne générale** est le point de départ : sans rien savoir, la meilleure prédiction est la note moyenne de tout le jeu.

L'**effet film** corrige ensuite : un film unanimement apprécié tire ses notes vers le haut quel que soit le spectateur.

L'**effet utilisateur** corrige enfin le biais personnel : certains mettent 5 à tout ce qu'ils aiment un peu, d'autres réservent le 5 à trois films dans leur vie.

### Pourquoi la régularisation est indispensable

Un film noté une seule fois, par un enthousiaste, obtiendrait un effet film énorme. C'est du bruit, pas du signal. La régularisation pénalise les effets estimés sur peu d'observations et les ramène vers zéro : un film vu dix fois pèse davantage qu'un film vu une fois.

Le paramètre de régularisation est choisi par validation, en balayant plusieurs valeurs et en retenant celle qui minimise l'erreur.

## Contenu du dépôt

| Chemin | Rôle |
| --- | --- |
| `Movies_recommander_systems.ipynb` | Construction du modèle, régularisation, évaluation |
| `Visualisation.ipynb` | Exploration du jeu : distribution des notes, genres, activité des utilisateurs |
| `data/` | MovieLens 100k, avec ses découpages d'origine |

Le jeu fournit ses propres partitions : `u1` à `u5` pour une validation croisée en cinq blocs, `ua` et `ub` pour un découpage entraînement/test. Les utiliser plutôt que de redécouper au hasard permet de comparer ses résultats à la littérature.

## Exécution

```bash
pip install pandas numpy matplotlib jupyter
jupyter notebook Movies_recommander_systems.ipynb
```

Les données sont incluses, rien à télécharger.

## Ce que ce modèle ne fait pas

Il ignore complètement le **contenu** des films. Les genres sont dans `u.item`, les données démographiques dans `u.user`, et rien de tout cela n'entre dans la prédiction. Un modèle hybride, croisant effets et similarité de contenu, serait la suite naturelle.

Il ne traite pas non plus le **démarrage à froid** : un nouvel utilisateur ou un nouveau film n'a aucun effet estimé, et retombe sur la moyenne générale.
