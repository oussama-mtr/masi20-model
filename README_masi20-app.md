# Pipeline ML — Prédiction des rendements du MASI20 \& Recommandation de portefeuille

Application Streamlit qui prédit les rendements mensuels des actions du MASI 20 (marché boursier marocain) à
l'aide de plusieurs modèles de machine learning, simule un portefeuille par backtest walk-forward, et produit
une recommandation d'allocation pour les mois à venir.

## Ce que fait l'application

* Charge des données techniques (mensuelles) et fondamentales (semestrielles) pour un univers d'actions du MASI 20.
* Construit un jeu de variables explicatives normalisées (momentum, moyennes mobiles, RSI, MACD, drawdown, ratios
fondamentaux ROA/ROE/P-E/P-B, etc.), sans données macroéconomiques.
* Compare 6 modèles de machine learning (Random Forest, XGBoost, LightGBM, CatBoost, SVR, MLP) et sélectionne le
meilleur uniquement sur un ensemble de validation, jamais sur l'ensemble de test.
* Réalise un backtest **walk-forward** : le modèle est réentraîné chaque mois sur toutes les données disponibles
à cette date, sans jamais utiliser d'information future (aucun look-ahead bias).
* Sélectionne chaque mois un Top N d'actions (paramétrable) avec une allocation **équipondérée** (poids égaux).
* Compare la performance du portefeuille à un **benchmark proxy** (rendement moyen équipondéré de tout l'univers
disponible ce mois-là — en l'absence d'une vraie série MASI/MASI20 dans les données).
* Distingue clairement les mois déjà réalisés (comparaison prédiction vs réalité) des mois à venir (prévision
seule, sans rendement réel connu).
* Exporte un rapport Excel complet, mis en forme (colonnes ajustées, pourcentages, montants en MAD).

## Structure du projet

```
masi20-app/
├── app.py              # Application Streamlit (point d'entrée unique)
├── requirements.txt    # Dépendances Python
└── README.md
```

## Prérequis

* Python 3.10 ou plus récent
* Un fichier Excel source contenant deux feuilles :

  * `Technique` : données mensuelles par action (Ticker, Date, Price, indicateurs techniques, colonne cible
`Rendement M+1 (Cible)`)
  * `Fondamental` : données semestrielles par action (Ticker, Période au format `S1 2024`/`S2 2024`, ratios
fondamentaux)

## Installation

```bash
# 1. Cloner le dépôt
git clone <url-du-depot>
cd masi20-app

# 2. Créer un environnement virtuel
python -m venv venv

# 3. Activer l'environnement virtuel
venv\\Scripts\\activate        # Windows
source venv/bin/activate     # macOS / Linux

# 4. Installer les dépendances
pip install -r requirements.txt
```

## Lancement

```bash
streamlit run app.py
```

L'application s'ouvre automatiquement dans le navigateur à l'adresse `http://localhost:8501`.

## Utilisation

1. Dans la barre latérale, charger le fichier Excel source (feuilles `Technique` et `Fondamental`).
2. Ajuster les paramètres si besoin : nombre d'actions sélectionnées (Top N), capital initial, taux sans
risque, années de training / validation / test / année en cours.
3. Cliquer sur **Lancer l'analyse**.
4. Parcourir les onglets : Validation, Test, Année en cours (backtest + prévision), Modèles \& features,
Diagnostic données.
5. Télécharger le rapport Excel complet via le bouton en bas de page.

