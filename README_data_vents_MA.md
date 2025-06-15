# 🌬️ Préparation des données de vent ERA5 pour la prédiction de sinistres Multirisque Agricole (MA)

## 🎯 Objectif

Ce projet vise à construire une base de données journalière des vents (ERA5) pour la France métropolitaine de **2012 à 2025**, agrégée à la maille **commune × jour**, afin de la croiser avec le portefeuille d'assurance **Multirisque Agricole (MA)** de Pacifica pour :

- Prédire la **probabilité journalière de sinistre** (modèle de classification)
- Estimer le **coût moyen** associé au sinistre (modèle de régression)

---

## 📚 Organisation des notebooks et rôle de chacun

### `01_recuperation_cds_data.ipynb`
✅ **Objectif** : automatiser le téléchargement des fichiers vent via l’API CDS pour chaque mois entre 2012 et 2025.
- Télécharge les données brutes `netcdf` pour chaque mois
- Extrait les variables utiles (u10, v10, gust...)
- Calcule les vitesses et exporte en `.csv` par heure
- Gère la reprise automatique via un log de traitement

### `02_commune_agregation.ipynb`
✅ **Objectif** : faire la **jointure spatiale** entre les points ERA5 et les communes INSEE, puis **agréger les vents à la maille journalière communale**.
- Lit le shapefile France et communes INSEE
- Attribue à chaque point ERA5 la commune dans laquelle il se trouve
- Agrège les vents (moyenne, max, min, std) par `INSEE_COM × date`
- Produit des fichiers `vent_annee_mois.csv` intermédiaires

### `03_concatenation_finale.ipynb`
✅ **Objectif** : fusionner tous les fichiers mensuels agrégés en un seul fichier final.
- Concatène tous les CSV du dossier `02_resultats_commune_agregation`
- Supprime les doublons, homogénéise les colonnes
- Sauvegarde finale : `data_vent_final.csv`

### `04_analyse_donnees.ipynb`
✅ **Objectif** : analyse exploratoire du jeu de données final ERA5 pour la France
- Affiche le nombre de communes et jours
- Contrôle la couverture spatiale
- Détection des valeurs extrêmes (boxplots, heatmaps)
- Cartographie des points de mesure et communes
- Normalisation des INSEE (4 chiffres → 5 chiffres)

---

## 🔍 Fichier principal obtenu

```
data_vent_final.csv
├─ INSEE_COM
├─ date
├─ wind_speed_10m_mean / max / min / std
├─ wind_speed_100m_mean / ...
├─ gust_mean / max / min / std
(...)
```

Chaque ligne = 1 jour × 1 commune

---

## 🌍 Données utilisées
- **ERA5** : base climatique du CDS, réanalyse horaire de vent
- **Shapefile INSEE** : limites administratives des communes métropolitaines

---

## ⚖️ Packages et leur utilité

| Package               | Utilité principale                                                                 |
|-----------------------|-------------------------------------------------------------------------------------|
| `cdsapi`              | Appels à l’API du Climate Data Store (ERA5) pour téléchargement NetCDF            |
| `netCDF4`             | Lecture des fichiers .nc et extraction des dimensions spatio-temporelles            |
| `numpy`               | Calculs scientifiques (vitesse du vent, normalisation)                             |
| `pandas`              | Manipulation de DataFrames, fusion CSV, groupby par jour/commune                   |
| `matplotlib / seaborn`| Visualisations statistiques (boxplots, cartographie thermique, histogrammes)       |
| `geopandas`           | Jointures spatiales avec shapefiles communaux INSEE                              |
| `shapely`             | Manipulation des géométries : points, polygones, union, intersection               |
| `glob`                | Lecture automatique des fichiers à traiter (batch)                                |
| `os / time`           | Accès aux fichiers, création de répertoires, temporisation                       |
| `sklearn.preprocessing` | Normalisation (MinMaxScaler) en vue de modélisation                            |

---

## 📊 Variables agrégées dans `data_vent_final.csv`

| Colonne                         | Description                                        |
|---------------------------------|----------------------------------------------------|
| `INSEE_COM`                     | Code commune INSEE (clé de jointure avec MA)       |
| `date`                          | Date journalière                                   |
| `wind_speed_10m_mean`          | Vitesse moyenne du vent à 10m                     |
| `wind_speed_10m_std`           | Écart-type à 10m                                 |
| `gust_max`, `gust_mean`        | Rafales maximales et moyennes                     |
| `wind_speed_10m_neutral_*`     | Idem mais en conditions neutres                   |
| `wind_speed_100m_*`            | Idem à 100 mètres                                 |

---

## 🚀 Prochaines étapes

- 🔗 **Croiser avec les sinistres MA** (par `INSEE_COM` et `date`)
- ✅ **Créer les cibles** :
  - `sinistre` : 0 ou 1 si sinistre a eu lieu
  - `montant_total` : somme des indemnités
  - `cout_moyen_sinistre` = `montant_total` / `nb_sinistres`
- 🌐 **Modélisation** à horizon 1 jour, en croisant avec les conditions météo

---

## 🌟 Auteurs
Projet dirigé par **Dimitri**, DAA / Travaux sinistres climatiques / Pacifica
