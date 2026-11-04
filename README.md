# Détection de fraude bancaire : projet Machine Learning (Projet 4)

Projet réalisé dans le cadre du module Machine Learning, Orange Digital Center.

**Objectif :** construire un modèle capable de détecter les transactions frauduleuses par carte bancaire, puis l'évaluer et l'interpréter du point de vue d'une banque.

## 📌 Sujet

Sujet distribué le 25 septembre 2026. Le groupe a choisi (au hasard) le **Projet 4 : Détection de fraude client**.

Chaque groupe doit fournir un notebook contenant toutes les étapes de la réalisation, avec détails et justifications.

## 👥 Équipe : groupe 3 (5 membres)

| Membre | Rôle |
|---|---|
| Fatou Fall | Exploration des données (EDA) |
| Fallou Mbaye | Prétraitement des données |
| Atou Diagne | Gestion du déséquilibre des classes |
| Adama Sarr | Modélisation |
| Maimouna Diallo | Évaluation, interprétation et documentation |

## 📊 Jeu de données

**Credit Card Fraud Detection** (Université Libre de Bruxelles et Worldline), disponible sur Kaggle :
https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud

- **284 807 transactions** de cartes bancaires de clients européens, sur **2 jours** de septembre 2013.
- Seulement **492 fraudes** (≈ 0,17 %) : fort déséquilibre des classes.
- **31 colonnes** :
  - `V1` à `V28` : variables anonymisées par ACP (analyse en composantes principales) pour protéger la vie privée des clients ;
  - `Time` : secondes écoulées depuis la première transaction du fichier ;
  - `Amount` : montant de la transaction ;
  - `Class` : variable cible (0 = normale, 1 = fraude).

Le fichier `creditcard.csv` (≈ 150 Mo) n'est **pas versionné** dans ce dépôt (voir `.gitignore`) en raison de sa taille. Il faut le télécharger depuis Kaggle et le placer dans `data/`.

## 🗂️ Structure du projet

```
detection-fraude-projet4/
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   └── creditcard.csv                   # à télécharger, non versionné
└── notebooks/
    └── projet4_detection_fraude.ipynb   # livrable principal
```

## ⚙️ Installation

Python **3.12** est requis (certaines versions du `requirements.txt` ne sont pas compatibles avec Python 3.13).

**Linux / macOS :**

```bash
# 1. Copier le projet
git clone https://github.com/Damstat/detection-fraude-projet4.git
cd detection-fraude-projet4

# 2. Créer et activer un environnement virtuel
python3.12 -m venv .venv
source .venv/bin/activate

# 3. Installer les bibliothèques
pip install --upgrade pip
pip install -r requirements.txt

# 4. Créer le dossier des données
mkdir data
```

**Windows (PowerShell) :**

```powershell
git clone https://github.com/Damstat/detection-fraude-projet4.git
cd detection-fraude-projet4
py -3.12 -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
mkdir data
```

> Si PowerShell refuse d'activer l'environnement (« l'exécution de scripts est désactivée »), lancer une fois `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`, puis réessayer.

Enfin, télécharger le dataset sur Kaggle (bouton **Download**), décompresser le fichier `.zip` et placer `creditcard.csv` dans le dossier `data/`.

## ▶️ Utilisation

1. Vérifier que `data/creditcard.csv` est bien présent.
2. Ouvrir le notebook :
   - dans **VS Code** : ouvrir `notebooks/projet4_detection_fraude.ipynb` et choisir le kernel `.venv` (Python 3.12) ;
   - ou avec **Jupyter** : `jupyter notebook notebooks/projet4_detection_fraude.ipynb`.
3. Exécuter toutes les cellules dans l'ordre (**Run All**). L'exécution complète prend environ 5 à 10 minutes.

## 🧭 Étapes du projet (plan du notebook)

1. **Contexte et problématique**
2. **Exploration des données (EDA)** : structure, valeurs manquantes, doublons, distribution de `Amount` et `Time`, déséquilibre des classes, corrélations.
3. **Prétraitement** : suppression des doublons, transformation de `Time` en `Hour`, découpage train/test stratifié (80/20), mise à l'échelle de `Amount` et `Hour` (RobustScaler).
4. **Gestion du déséquilibre** : comparaison de 4 stratégies (`class_weight`, SMOTE, sous-échantillonnage, SMOTE + Tomek Links). **SMOTE** retenu, appliqué uniquement au jeu d'entraînement.
5. **Modélisation** : régression logistique, arbre de décision, Random Forest, XGBoost, LightGBM. **XGBoost** retenu, puis ajustement du seuil de décision sur un jeu de validation.
6. **Évaluation et interprétation** : matrice de confusion finale, importance des variables, SHAP, analyse des fraudes ratées, choix du seuil selon le coût métier.
7. **Conclusion** : enseignements, limites et pistes d'amélioration.

## 📏 Métriques d'évaluation

Vu le fort déséquilibre des classes, l'**accuracy n'est pas utilisée** comme métrique principale : un modèle qui ne détecte aucune fraude obtiendrait déjà 99,83 %. Le projet s'appuie sur :

- la **précision** (quand le modèle signale une fraude, a-t-il raison ?) ;
- le **rappel** (parmi les vraies fraudes, combien sont détectées ?) ;
- le **F1-score**, la **PR-AUC** (métrique principale) et la ROC-AUC ;
- la **matrice de confusion** ;
- un **coût métier** : montant des fraudes ratées + coût de vérification des alertes.

Toutes les mesures sont faites sur le **jeu de test**, qui n'a jamais été rééquilibré.

## 📈 Résultats principaux

Modèle retenu : **XGBoost**, entraîné sur des données rééquilibrées par SMOTE et évalué sur un jeu de test non modifié (56 746 transactions, dont 95 fraudes).

| Seuil de décision | Fraudes détectées | Fraudes ratées | Fausses alertes | Coût total* |
|---|---|---|---|---|
| 0,5 (par défaut) | 74 / 95 | 21 | 17 | 6 289,62 |
| 0,977 (maximise le F1) | 68 / 95 | 27 | 1 | 6 222,10 |
| 0,35 (minimise le coût métier) | 75 / 95 | 20 | 28 | **4 539,94** |

\* Coût total = montant des fraudes ratées + 5 par alerte (coût de vérification supposé). PR-AUC du modèle : **0,811**.

- **Variable la plus influente :** `V14` (59,7 % de l'importance). Ses valeurs basses orientent fortement vers la fraude (analyse SHAP).
- **Fraudes ratées :** elles ressemblent à des transactions normales. 16 sur 27 sont indétectables par ce modèle, quel que soit le seuil.
- **Choix du seuil :** c'est une décision métier. En tenant compte du coût réel des erreurs, un seuil bas (≈ 0,35 à 0,5) réduit les pertes d'environ 27 % par rapport au seuil F1.

Les détails, les limites (peu de fraudes, variables anonymisées, coûts supposés, données de 2013) et les pistes d'amélioration sont présentés dans la conclusion du notebook.

## 🔄 Méthode de travail

- Chaque membre a réalisé sa partie à la suite des autres, dans le même notebook.
- Un commit explicite après chaque micro-tâche accomplie (pas d'attente de fin de partie).
- Relecture croisée avant la remise finale : chaque membre doit être en mesure d'expliquer l'ensemble du projet.

## 📅 Statut

✅ Projet terminé : EDA, prétraitement, gestion du déséquilibre, modélisation, évaluation et interprétation.
