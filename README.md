# Prévision des ventes Walmart (M5) avec LightGBM

🇫🇷 [Français](#-français) · 🇬🇧 [English](#-english)

---

## 🇫🇷 Français

### Objectif
Prévoir les ventes journalières de 30 490 produits Walmart (3 États, 10 magasins) pour les 28 jours suivants.

### Données
[M5 Forecasting – Accuracy](https://www.kaggle.com/competitions/m5-forecasting-accuracy) : ventes journalières Walmart de 2011 à 2016, calendrier (événements, jours SNAP) et prix de vente. Les fichiers CSV ne sont pas dans ce dépôt, il faut les télécharger sur la page de la compétition.

### Étapes
1. Chargement et passage au format long : une ligne = un produit un jour (**31,8 millions de lignes**, 0,38 Go grâce à des types optimisés)
2. Ajout du calendrier et des prix ; suppression des jours où le produit n'était pas en vente
3. Variables du passé : retards de 28, 35 et 42 jours, moyennes mobiles (7, 28, 60 jours), volatilité, variation de prix
4. Séparation **par date** (pas au hasard) pour éviter la fuite de données ; tous les retards font au moins 28 jours
5. Backtest sur une deuxième période pour vérifier que le gain est stable
6. Modèle **LightGBM** (objectif Tweedie, adapté aux ventes souvent nulles) avec early stopping
7. Comparaison avec des modèles simples, importance des variables, fichier de soumission

### Résultats

| Période testée | Moyenne des 28 derniers jours (RMSE) | **LightGBM** (RMSE) | Gain |
|---|---|---|---|
| Jours 1886–1913 (backtest) | 2,298 | **2,107** | **-8,3 %** |
| Jours 1914–1941 (validation) | 2,323 | **2,152** | **-7,4 %** |

(Pour comparaison, prédire toujours 0 donne un RMSE de 3,916.)

- LightGBM réduit l'erreur de **7 à 8 %** par rapport à la moyenne des 28 derniers jours, **sur les deux périodes** : le gain est stable.
- Les variables les plus importantes sont les moyennes mobiles (60 et 28 jours), puis le produit et le jour de la semaine : la préparation des variables compte plus que le choix du modèle.
- Le modèle suit bien le rythme de la semaine mais sous-estime les pics de ventes.

### Limites
Validation sur deux périodes de 28 jours seulement ; comparaison en RMSE (la métrique officielle M5 est le WRMSSE) ; les pics de ventes sont sous-estimés.

### Outils
Python, pandas, NumPy, LightGBM, Matplotlib

### Lancer le projet
```bash
pip install -r requirements.txt
# télécharger les fichiers de la compétition M5 (calendar.csv, sales_train_evaluation.csv, sell_prices.csv, sample_submission.csv)
jupyter notebook m5_walmart_sales_forecasting.ipynb
```
Le notebook a été exécuté sur une machine avec 30 Go de RAM.

---

## 🇬🇧 English

### Goal
Forecast the daily sales of 30,490 Walmart products (3 states, 10 stores) for the next 28 days.

### Data
[M5 Forecasting – Accuracy](https://www.kaggle.com/competitions/m5-forecasting-accuracy): Walmart daily sales from 2011 to 2016, calendar (events, SNAP days) and sell prices. The CSV files are not included; download them from the competition page.

### Main results
- Data reshaped to **31.8 million rows** (one product per day), with optimized memory usage.
- Split **by date** and lags of at least 28 days to avoid data leakage, plus a backtest on a second period.
- **LightGBM** (Tweedie objective) reduces the RMSE by **8.3%** on the backtest period (2.107 vs 2.298) and by **7.4%** on the validation period (2.152 vs 2.323) compared to a 28-day moving average: the gain is stable.
- Feature engineering mattered more than the choice of model: rolling means are by far the most important features.

**Author:** Sezen Nisa Dikmen
