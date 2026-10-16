# Log Pipeline LARSI — Détection d'intrusions sur les serveurs web

Pipeline complet de **Data Engineering** en Python pour transformer des fichiers journaux (logs) bruts, massifs et hétérogènes de serveurs web Apache en un actif de données structuré, propre et exploitable par des modèles de Machine Learning, dans le cadre d'un stage de fin de Licence 3 en Intelligence Artificielle et Big Data au **LARSI** (Laboratoire de Recherche en Sciences de l'Ingénieur, Université de Lomé).

## Objectif

Transformer des journaux bruts non structurés (Apache Access, Apache Error, Syslog) en un fichier CSV tabulaire propre et standardisé, enrichi de caractéristiques comportementales et statistiques, afin de servir de fondation à des analyses de **détection d'anomalies et d'intrusions**.

## Architecture du pipeline (ETL)

```
Extract ──► Log_Parser.ipynb      Ingestion récursive multi-format + parsing orienté objet
Transform ─► Log_Cleaner.ipynb    Nettoyage, normalisation ISO 8601 (UTC), déduplication
           └ Log_Visualizer.ipynb Ingénierie des caractéristiques, encodage, statistiques, graphiques
Load ─────► Combined_Access_Logs.csv / Access_log_cleaned.csv
```

### Modules

| Notebook | Rôle |
|----------|------|
| `Log_Parser.ipynb` | Ingestion récursive des fichiers logs (`.gz` inclus), auto-détection des formats (Apache Access, Apache Error, Syslog), extraction des champs clés par expressions régulières |
| `Log_Cleaner.ipynb` | Imputation des valeurs manquantes (`unknown`), normalisation temporelle ISO 8601 / UTC, tri chronologique, déduplication, ingénierie des caractéristiques, encodage, transformation logarithmique, standardisation |
| `Log_Visualizer.ipynb` | Analyse exploratoire (EDA), distribution temporelle, méthodes HTTP, codes de statut, référents, User-Agents, matrice de corrélation, clustering hiérarchique, analyse en composantes principales |
| `logtransformer.ipynb` | Transformation logarithmique ciblée des distributions asymétriques |

## Ingénierie des caractéristiques

Les caractéristiques extraites couvrent plusieurs dimensions comportementales :

- **Temporelles** : encodage cyclique de l'heure (`sin`/`cos`), temps normalisé en secondes ;
- **IP** : nombre de requêtes, étalement temporel, taux de requêtes/seconde, maximum de requêtes par seconde (`ip_max_requests_per_sec`), régularité des intervalles, activité de sous-réseau, diversité des méthodes, pays d'origine (GeoIP) ;
- **URL / Référent** : profondeur de chemin, entropie de Shannon, caractères spéciaux, paramètres de requête et de fragment ;
- **User-Agent** : longueur, entropie, caractères spéciaux, détection de bots ;
- **Encodage** : rareté des méthodes (`method_encoded`), du pays (`country_encoded`), catégorie de statut HTTP, logarithme `log(1+x)`, standardisation centrée-réduite.

## Résultats clés

- **42 899 lignes** brutes consolidées → **41 433 lignes** propres après nettoyage ;
- **62 colonnes** finales, dont **57 variables numériques standardisées** ;
- 20 composantes principales suffisent pour expliquer **99 % de la variance**.

## Prérequis

- Python 3.12+
- `pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`, `scipy`, `geoip2`

```bash
pip install pandas numpy scikit-learn matplotlib seaborn scipy geoip2
```

La variable `country` utilise la base gratuite **DB-IP Lite Country** (format `.mmdb`, licence CC BY 4.0) : télécharger le fichier du mois sur https://db-ip.com/db/download/ip-to-country-lite, le décompresser et renseigner son chemin dans `GEOIP_DB` (`Log_Cleaner.ipynb`, cellule « Chemins »). Sans la base, `country` vaut `unknown`.

IP Geolocation by [DB-IP](https://db-ip.com).

## Structure du dépôt

```
code/              # Notebooks du pipeline
  Log_Parser.ipynb
  Log_Cleaner.ipynb
  Log_Visualizer.ipynb
  logtransformer.ipynb
  Readme.ipynb
.gitignore
README.md
```

> Les données brutes (`data/`), les sorties (`logs_output/`) et l'environnement virtuel (`venv/`) sont exclus du dépôt.

## Auteur

Rapport de stage — Licence 3 Intelligence Artificielle & Big Data, École Polytechnique de Lomé, Université de Lomé (LARSI).
