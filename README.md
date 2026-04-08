# 🤼 WWE Match Data — Scraping & Analyse

> Collecte automatisée de **40 195 matchs WWE** depuis [profightdb.com](http://www.profightdb.com) et analyse statistique interactive avec Plotly.

---

## 📋 Table des matières

- [Aperçu du projet](#aperçu-du-projet)
- [Stack technique](#stack-technique)
- [Structure du projet](#structure-du-projet)
- [Installation](#installation)
- [Étapes du notebook](#étapes-du-notebook)
- [Données collectées](#données-collectées)
- [Analyses et visualisations](#analyses-et-visualisations)
- [Résultats notables](#résultats-notables)
- [Limitations](#limitations)

---

## 🎯 Aperçu du projet

Ce projet scrappe l'intégralité des cartes WWE disponibles sur **profightdb.com** (735 pages) pour constituer une base de données de matchs, puis en effectue une analyse statistique et visuelle approfondie.

Les grandes étapes :

1. **Scraping de la liste des événements** — dates, promotions, noms et lieux depuis les pages de listing
2. **Scraping de chaque événement** — détail des matchs (catcheurs, résultats, durées, titres)
3. **Nettoyage** — conversion des types, séparation ville/état, enrichissement
4. **Analyse & visualisations** — graphiques interactifs Plotly sur les matchs, lutteurs et titres

---

## 🛠️ Stack technique

| Catégorie | Outils |
|-----------|--------|
| Langage | Python 3.12 |
| Scraping | `requests`, `BeautifulSoup4` |
| Traitement | `pandas` |
| Visualisation | `plotly.express` |
| Notebook | `Jupyter Notebook` |
| Stockage | CSV |

---

## 📁 Structure du projet

```
wwe-scraping-analyse/
│
├── Scraping_wwe.ipynb      # Notebook principal (scraping + nettoyage + analyse)
├── wwe_scraping.csv        # Dataset final (~40 000 lignes)
└── README.md
```

---

## ⚙️ Installation

```bash
# Cloner le dépôt
git clone https://github.com/username/wwe-scraping-analyse.git
cd wwe-scraping-analyse

# Créer un environnement virtuel
python -m venv data_env
source data_env/bin/activate  # Windows : data_env\Scripts\activate

# Installer les dépendances
pip install requests beautifulsoup4 html5lib pandas plotly jupyter
```

---

## 📓 Étapes du notebook

### Étape 1 — Scraping de la page de listing

Extraction depuis `profightdb.com/cards/wwe-cards-pgN-no-2.html` des dates (via les liens `/this-day-in-history/`), promotions (balises `<strong>`), noms d'événements (liens `/cards/wwe/`) et lieux (liens `/locations/united-states/`).

### Étape 2 — Scraping de chaque événement

Pour chaque URL d'événement, parsing du tableau `div.table-wrapper table` pour extraire ligne par ligne : le vainqueur, les perdants, le type de résultat, la durée, le type de match et le titre en jeu.

Exemple de sortie :
```python
{'winner': 'CM Punk', 'losers': ['Jey Uso'], 'result': 'def. (pin)',
 'duration': '20:58', 'match_type': '', 'title': 'WWE World Heavyweight Championship(title change)'}
```

### Étape 3 — Scraping complet (735 pages)

Boucle sur les 735 pages avec une pause `time.sleep(1)` entre chaque requête pour ne pas surcharger le serveur. Gestion des erreurs HTTP. Export final en CSV encodé UTF-8-sig.

**Résultat : 40 195 matchs collectés.**

### Étape 4 — Nettoyage

- Séparation de la colonne `location` en `city` et `state`
- Conversion de `date` en `datetime64`
- Conversion de `duration` en `timedelta64`
- Ajout de colonnes dérivées : `state_count`, `winner_count`, `loser_count`, `year`

---

## 📦 Données collectées

| Colonne | Type | Remplissage | Description |
|---------|------|-------------|-------------|
| `date` | datetime64 | 100% | Date du match |
| `promotion` | object | 100% | Promotion (WWE) |
| `city` | object | 84% | Ville |
| `state` | object | 84% | État américain |
| `event_name` | object | 100% | Nom de l'événement |
| `winner` | object | ~100% | Vainqueur du match |
| `losers` | object | ~100% | Perdant(s), séparés par virgule |
| `result` | object | ~100% | Type de résultat |
| `duration` | timedelta64 | 41% | Durée du match |
| `match_type` | object | 25% | Type spécial de match |
| `title` | object | 18% | Titre en jeu |

---

## 📊 Analyses et visualisations

Toutes les visualisations sont réalisées avec **Plotly** (graphiques interactifs).

**Géographie**
- Nombre de matchs par État américain
- Durée moyenne des matchs par État

**Temporalité**
- Évolution du nombre de matchs par année (2002–2025)
- Évolution des 5 types de match les plus fréquents par année
- Évolution des 5 titres les plus disputés par année

**Catcheurs**
- Top 20 des vainqueurs et des perdants les plus fréquents
- Top 10 des catcheurs les plus actifs (toutes années confondues)
- Top 10 des catcheurs les plus actifs sur la période 2002–2010
- Heatmap d'activité des 10 catcheurs les plus actifs par année
- Courbe de victoires annuelles pour John Cena, Edge et The Undertaker

**Titres**
- Top 15 des titres WWE avec le plus de matchs disputés
- Top vainqueur pour chacun des 15 titres les plus défendus

**Types de finish**
- Top 5 des types de fin de match (`def. (pin)`, `def.`, `def. (sub)`, `def. (DQ)`, `draw (NC)`)

---

## 🏆 Résultats notables

- **40 195 matchs** collectés sur l'ensemble de l'historique WWE disponible sur le site
- **John Cena** est le catcheur avec le plus de victoires, devant Randy Orton et Jey Uso
- **The Miz** apparaît le plus souvent côté perdants
- Le finish le plus courant est la victoire **par tombé** (`def. (pin)`)
- Le titre le plus souvent défendu est le **WWE United States Championship** (930 matchs), suivi de l'**Intercontinental Championship** (908)
- **John Cena** detient le record de victoires pour le WWE Championship et l'US Championship ; **Gunther** pour l'Intercontinental ; **Seth Rollins** pour le World Heavyweight Championship
- Le pic d'activité WWE se situe autour de **2013–2015**, avec plus de 1 200 matchs recensés par an

---

## ⚠️ Limitations

- Le scraping dépend de la structure HTML de profightdb.com — toute modification du site peut casser le parser
- La colonne `duration` est absente pour ~60% des matchs (données manquantes à la source)
- La colonne `location` couvre principalement les États-Unis ; les événements internationaux ont souvent un lieu vide
- Deux pages ont retourné des erreurs HTTP durant le scraping (pages 34 et 39) et ont été ignorées
- Projet à usage **éducatif et analytique** uniquement — les données appartiennent à leurs sources respectives

---

*Projet réalisé dans le cadre d'un apprentissage du web scraping et de l'analyse de données avec Python.*
