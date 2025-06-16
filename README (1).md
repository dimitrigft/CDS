# Interprétabilité des Clauses Contractuelles

## 📌 Objectif

Ce projet vise à construire un modèle de Machine Learning capable de déterminer automatiquement si une clause contractuelle extraite d’un contrat d’assurance est **interprétable** (formulation floue ou ambigüe) ou **non-interprétable** (formulation claire).

---

## 📁 Données

Les données proviennent de trois sources :
- Clauses interprétables du **Médiateur de l’assurance**,
- Clauses non-interprétables extraites de **contrats PDF de Pacifica**,
- Clauses générées manuellement.

Le jeu de données final contient **596 lignes** et **3 colonnes** :
- `clauses` : texte brut de la clause,
- `interpretabilite` : 1 (interprétable) ou 0 (non-interprétable),
- `clauses_vect` : clause nettoyée.

Un déséquilibre de classes a été corrigé avec **SMOTE**.

---

## 🔄 Pipeline

1. **Nettoyage du texte** : suppression HTML, stopwords, lemmatisation.
2. **Vectorisation** : avec Word2Vec.
3. **Équilibrage des classes** : avec SMOTE.
4. **Modélisation** : plusieurs algorithmes ont été testés.
5. **Évaluation** : métriques classiques (f1-score, ROC AUC, matrice de confusion).

---

## 🤖 Modèles testés

- Régression logistique
- Random Forest
- Gradient Boosting
- XGBoost (meilleur résultat)
- SVM

---

## 🧪 Résultats

- Le modèle **XGBoost** a obtenu les meilleures performances sur le jeu de test.
- Les modèles détectent bien les clauses non-interprétables, mais ont plus de mal avec les clauses interprétables.
- La faible quantité de données reste une limite.

---

## 🧰 Librairies utilisées

- `pandas`, `numpy`
- `scikit-learn`
- `xgboost`
- `nltk`, `gensim`
- `imblearn`
- `matplotlib`, `seaborn`

---

## ▶️ Lancer le projet

1. Cloner le dépôt  
2. Installer les dépendances  
3. Ouvrir le notebook `interpretabilite_clauses.ipynb`  
4. Exécuter les cellules dans l’ordre