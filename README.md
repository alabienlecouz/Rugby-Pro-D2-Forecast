# Rugby Pro D2 — Prévision de classement

Prédiction du classement final de Pro D2 2026/2027 par simulation Monte-Carlo, à partir des forces attaque/défense estimées sur les saisons N, N-1 et N-2.

## Barème

Match joué → points de classement Pro D2 :

* victoire = 4, nul = 2, défaite = 0
* bonus offensif : +1 si |essais\_dom − essais\_ext| ≥ 3 (en faveur de l'équipe qui en marque plus)
* bonus défensif : +1 si défaite avec |score\_dom − score\_ext| ≤ 5

## Forces par équipe

Pour une saison donnée, avec $M$ l'ensemble des matchs joués :

$$

\text{Attaque}_i = \frac{\sum \text{marqués}_i^{dom} + \sum \text{marqués}_i^{ext}}{n_i^{dom} + n_i^{ext}}
,\ 
\text{Défense}_i = \frac{\sum \text{encaissés}_i^{dom} + \sum \text{encaissés}_i^{ext}}{n_i^{dom} + n_i^{ext}}

$$

## Pondération inter-saisons

$$
\text{Attaque}_i^{finale} = \frac{w_N A_i^{(N)} + w_{N-1} A_i^{(N-1)} + w_{N-2} A_i^{(N-2)}}{w_N + w_{N-1} + w_{N-2}}
$$

avec $(w_N, w_{N-1}, w_{N-2}) = (0.5, 1.0, 0.5)$ si moins de 5 matchs joués par équipe, sinon $(1.0, 0.4, 0.1)$. Idem pour la défense.

Normalisation sur la moyenne de référence $\mu$ (moyenne des scores de la saison N-1) :

$$
\text{Attaque}_i \leftarrow \text{Attaque}_i \cdot \frac{\mu}{\overline{\text{Attaque}}}, \qquad \text{Défense}_i \leftarrow \text{Défense}_i \cdot \frac{\mu}{\overline{\text{Défense}}}
$$

## Simulation

Pour un match restant $i$ (dom) vs $j$ (ext) :

$$
\lambda_{ij} = \frac{\text{Attaque}_i \cdot \text{Défense}_j}{\mu}, \qquad \lambda_{ji} = \frac{\text{Attaque}_j \cdot \text{Défense}_i}{\mu}, \qquad \lambda \in [0.1,\ 50]
$$

$$
S_{ij} \sim \text{Poisson}(\lambda_{ij}), \qquad S_{ji} \sim \text{Poisson}(\lambda_{ji})
$$

Chaque simulation : tirage des scores de tous les matchs restants, attribution des points de classement (victoire/nul + bonus défensif). $n = 1000$ simulations.

## Classement attendu

$$
\mathbb{E}[\text{Points}_i] = \text{Points}_i^{déjà\ acquis} + \frac{1}{n}\sum_{s=1}^{n} \text{Points}_i^{(s)}
$$

Tri décroissant → `Expected_Rank`, exporté dans `expected_rank_j8.csv`.

## Fichiers

* `ProD2-26-27.ipynb` — prévision saison en cours (données live : [Rugby-Data](https://github.com/transientlunatic/Rugby-Data))
* `labo25_26.ipynb` — backtest de la méthode sur N-1
* `matchs_N-1.csv`, `matchs_N-2.csv` — historiques

