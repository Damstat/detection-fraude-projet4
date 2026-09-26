# Détection de Fraude — Projet Machine Learning (Projet 4)

Projet réalisé dans le cadre du module Machine Learning — Orange digital center .

Objectif : construire un modèle capable de détecter des transactions frauduleuses.

## 📌 Sujet

Sujet distribué le 25 septembre 2026. Le groupe a choisi (au hasard) le **Projet 4 : Détection de fraude client**.

Chaque groupe doit fournir un notebook contenant toutes les étapes de la réalisation, avec détails et justifications.

## 👥 Équipe groupe 3 (5 membres)

| Membre | Rôle |
|---|---|
| Membre 1 | Exploration des données (EDA) |
| Membre 2 | Prétraitement des données |
| Membre 3 | Gestion du déséquilibre des classes |
| Membre 4 | Modélisation |
| Membre 5 | Évaluation, interprétation & documentation |

*(à compléter avec les noms réels des membres)*

## 📊 Jeu de données

**Credit Card Fraud Detection** (ULB) — disponible sur Kaggle :
https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud

- ~284 807 transactions de cartes bancaires européennes (septembre 2013)
- 492 transactions frauduleuses seulement (~0,17 % des cas → fort déséquilibre de classes)
- Variables `V1` à `V28` anonymisées via PCA, plus `Time`, `Amount` et `Class` (0 = normale, 1 = fraude)

Le fichier `creditcard.csv` n'est pas versionné dans ce dépôt (voir `.gitignore`) en raison de sa taille. Le télécharger depuis Kaggle et le placer dans `data/`.

## 🗂️ Structure du projet

```
detection-fraude-projet4/
├── README.md
├── requirements.txt
├── data/
│   └── creditcard.csv          # à télécharger, non versionné
├── notebooks/
│   └── projet4_detection_fraude.ipynb   # livrable principal
└── .gitignore
```

## ⚙️ Installation

```bash
git clone https://github.com/<utilisateur>/detection-fraude-projet4.git
cd detection-fraude-projet4
python3.12 -m .env venv
source venv/bin/activate   
# sous Windows : .env\Scripts\activate
pip install -r requirements.txt
```

## ▶️ Utilisation

1. Télécharger le dataset depuis Kaggle et le placer dans `data/creditcard.csv`
2. Lancer Jupyter :
   ```bash
   jupyter notebook notebooks/projet4_detection_fraude.ipynb
   ```
3. Exécuter les cellules dans l'ordre

## 🧭 Étapes du projet (plan du notebook)

1. Contexte & problématique
2. Chargement et exploration des données (EDA)
3. Analyse du déséquilibre de classes
4. Prétraitement (scaling, split train/test stratifié)
5. Gestion du déséquilibre (SMOTE, undersampling, class_weight…)
6. Modélisation (Logistic Regression, Random Forest, XGBoost…)
7. Évaluation (Precision, Recall, F1, AUC-ROC, matrice de confusion)
8. Interprétation & conclusion

## 📏 Métriques d'évaluation

Vu le fort déséquilibre des classes, l'accuracy n'est **pas** utilisée comme métrique principale. Le projet s'appuie sur :
- Precision, Recall, F1-score (classe minoritaire = fraude)
- AUC-ROC et courbe Precision-Recall
- Matrice de confusion

## 🔄 Méthode de travail

- Une branche Git par tâche/membre, fusion via Pull Request vers `main`
- Un commit explicite après chaque tâche accomplie (pas d'attente de fin de partie)
- Relecture croisée avant la remise finale : chaque membre doit être en mesure d'expliquer l'ensemble du projet

## 📅 Statut

🚧 En cours de réalisation.
