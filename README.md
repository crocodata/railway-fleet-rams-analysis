# RAMS Analysis & Failure Prediction – Passenger Train Fleet
### Analyse de fiabilité et prédiction des pannes – flotte de 30 rames voyageurs

> **Données synthétiques** générées pour l'exercice : elles ne proviennent d'aucun exploitant réel.

## Context & questions – Contexte et questions
Historique de maintenance de 30 rames voyageurs sur 8 systèmes (portes, climatisation, freinage, toilettes, pantographe, traction, signalisation embarquée, bogies), de janvier 2023 à décembre 2025 : 4 053 interventions après nettoyage.

1. Quels systèmes et quelles rames sont les moins fiables ?
2. Le respect du plan de maintenance préventive réduit-il les pannes ?
3. Comment l'organisation de la maintenance pèse-t-elle sur la disponibilité ?

## Approach – Démarche
| Étape | Notebook | Contenu |
|---|---|---|
| 1. Nettoyage | `01-nettoyage-railway.ipynb` | Doublons supprimés, catégories harmonisées, dates au format mixte converties, valeurs impossibles neutralisées (durées négatives, coûts aberrants, immobilisations incohérentes, types d'intervention mal étiquetés). Aucune intervention réelle supprimée, aucune valeur inventée, chaque correction tracée. |
| 2. Exploration RAMS | `02-exploration-railway.ipynb` | MTTR, MDT, MTBF, Pareto, disponibilité, saisonnalité, analyses par rame et par équipe, analyse de Weibull avec données censurées. |
| 3. Modélisation | `03-modelisation-railway.ipynb` | Prédiction d'une panne à 30 jours, découpage temporel (apprentissage 2023-2024, test 2025), comparaison d'une référence par système, d'une régression logistique et d'une forêt aléatoire. |

## Key results – Résultats clés
- **Les portes coûtent le plus d'heures** : 4 843 h d'immobilisation (≈ 25 % du total), à cause de leur fréquence de panne (MTBF ≈ 98 jours par rame), pas de leur gravité. Les pertes restent dispersées : 5 systèmes sur 8 pour atteindre 80 % des heures perdues.
- **Disponibilité** : chaque système dépasse 99 %, mais une rame (systèmes en série) tombe à ≈ 97,5 %, soit ≈ 9 jours d'immobilisation par an et par rame, ou l'équivalent de ≈ 0,7 rame en permanence à l'arrêt (hors préventives).
- **L'attente domine le temps d'arrêt** : 10 à 15 h par panne quel que soit le système, soit 60 à 90 % de l'immobilisation. Les équipes étant polyvalentes, l'approvisionnement en pièces semble plus en cause que la charge.
- **Deux saisonnalités** : climatisation en été (≈ ×4 de juin à août), freinage en hiver (givre, humidité, adhérence).
- **Fortes disparités entre rames** : de 18 à 68 pannes. Les écarts viennent des correctives (CV 30 % contre 5 % pour les préventives). R010 et R018 sont pénalisées sur tous leurs systèmes.
- **Pas d'usure démontrée** : paramètres de Weibull compatibles avec β = 1 (estimation non fiable pour les bogies, faute de pannes). Résultat confirmé par trois méthodes : MTBF, Weibull et taux de panne selon l'avancement du cycle préventif.
- **Modèle** : AUC ROC ≈ 0,70, comparable à une simple référence par système ; la régression logistique améliore l'AUC PR d'environ 10 % (0,258 contre 0,235). Le risque dépend surtout du système, de la saison et de l'historique de la rame.
- **Retard de préventive non mesurable** : il ne concerne que 1,6 % des situations. Le taux de panne passe de 13 % à 21 % juste après l'échéance, sans significativité statistique.
- **En pratique** : cibler les portes double le taux de pannes détectées (lift ≈ 2,3). Le modèle n'apporte un avantage qu'au-delà (lift de 1,86 contre 1,76 pour 30 % d'inspections).

## Recommendations – Recommandations
1. **Réduire l'attente** (stock de pièces, délais d'approvisionnement, créneaux atelier) : le levier le plus large.
2. **Fiabiliser les portes**, premier système en heures perdues.
3. **Campagnes préventives saisonnières** : climatisation au printemps, freinage à l'automne, déclenchées par la saison plutôt que par l'âge.
4. **Examiner R010 et R018 dans leur ensemble** (âge, historique, affectation), leurs pannes touchant tous les systèmes.
5. **Tester un allongement de l'intervalle préventif** sur quelques rames pilotes : le risque n'augmente pas à l'approche de l'échéance, mais les données ne permettent pas d'extrapoler au-delà.
6. **Compléter la collecte** : stocks et délais de pièces, kilométrage, capacité des ateliers, contrôles à la saisie.

## Limitations – Limites
- Données synthétiques.
- MTBF en jours calendaires, faute de kilométrage ou d'heures de service.
- Hypothèse « réparé comme neuf » dans l'analyse de Weibull.
- Très peu de situations de retard préventif : l'effet du retard ne peut être ni confirmé ni exclu.
- Cause de l'attente supposée, faute de données de stock ; capacité des ateliers inconnue.

## Skills – Compétences mobilisées
Python (pandas, numpy), visualisation (matplotlib, seaborn), indicateurs RAMS (MTTR, MDT, MTBF, disponibilité), analyse de survie (Weibull avec censure, lifelines), scikit-learn (régression logistique, forêt aléatoire), évaluation de modèles sur classes déséquilibrées (AUC PR, lift), découpage temporel contre les fuites de données.

## Repository structure – Structure du dépôt
```
├── README.md
├── data/
│   ├── maintenance_rames_2023_2025.csv   # données brutes (synthétiques)
│   └── maintenance_rames_clean.csv       # données nettoyées
├── notebooks/
│   ├── 01-nettoyage-railway.ipynb
│   ├── 02-exploration-railway.ipynb
│   └── 03-modelisation-railway.ipynb
└── requirements.txt
```

## Run – Exécution
```bash
pip install -r requirements.txt
```
Dans les notebooks, remplacez les chemins Kaggle (`/kaggle/input/...`) par `../data/...`.
