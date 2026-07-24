# Japan Protein Cost-Efficiency Analysis

**Which protein sources give you the most value for your yen in Japan?**

A data analysis project examining 25 common protein sources available in Japanese supermarkets, ranked by cost-efficiency, protein quality, and muscle-building value.

📊 **[View the Tableau Dashboard](https://public.tableau.com/app/profile/anthony.disorbo/viz/WhatstheBestValueProteininJapan/Story1)**

---

## Project Overview

Most protein cost comparisons stop at price per gram of protein. This project goes further:

1. **Raw cost efficiency** — ¥ per gram of protein (simple price comparison)
2. **Quality-adjusted cost** — adjusted for how well your body actually absorbs each protein (DIAAS score)
3. **Muscle-building efficiency** — ranked by leucine content and protein quality combined (for athletes and bodybuilders)
4. **Smart Shopper tool** — at what supermarket price does each food become a good deal?
5. **Amino acid analysis** — which proteins are complete? Which amino acids are limiting?

### Key findings
- **Chicken eggs** rank #1 overall after quality adjustment — affordable, complete, and high DIAAS
- **Natto** is the best value plant protein and delivers more leucine per ¥100 than whey protein supplements
- **Wagyu beef** is the worst value regardless of metric — beautiful food, poor protein economics
- **Whole milk** ranks #1 for muscle-building value per yen, but requires 836g per meal to hit the leucine threshold
- **21 of 25 foods** are complete proteins meeting all FAO indispensable amino acid requirements

---

## Data Sources

| Source | Data | Date |
|---|---|---|
| [USDA FoodData Central — SR Legacy](https://fdc.nal.usda.gov/download-datasets.html) | Nutrient composition per 100g | April 2018 release |
| [e-Stat Retail Price Survey](https://www.e-stat.go.jp/) | Japanese supermarket prices | June 2026 |
| [FAO/WHO (2013)](https://www.fao.org/publications/card/en/c/ab5c9e53-4be2-5452-9b72-16bb48a9e32e/) | DIAAS protein quality scores | 2013 |
| [Gilmour et al. — Fonterra](https://doi.org/10.3390/foods10040880) | Whey protein amino acid composition (WPC-C) | 2021 |
| Personal market observation | Prices for specific cuts and specialty items | June 2026 |

---

## Project Structure

```
japan-protein-analysis/
│
├── README.md
├── notebooks/
│   ├── japan_protein_analysis_01_eda.ipynb
│   ├── japan_protein_analysis_02_prices.ipynb
│   ├── japan_protein_analysis_03_diaas.ipynb
│   ├── japan_protein_analysis_03b_amino_acid.ipynb
│   ├── japan_protein_analysis_04_deeper_analysis.ipynb
│   └── japan_protein_analysis_05_tableau_prep.ipynb
├── data/
│   ├── raw/                              # USDA SR Legacy files — not included (see Setup)
│   └── processed/
│       ├── aa_merged.csv
│       ├── nutrients_analysis.csv
│       ├── nutrients_base.csv
│       ├── nutrients_diaas.csv
│       ├── nutrients_prices.csv
│       ├── tableau_aa_long.csv
│       ├── tableau_long_ranking.csv
│       ├── tableau_main.csv
│       ├── tableau_muscle_smart_shopper.csv
│       └── tableau_smart_shopper.csv
└── charts/
    ├── aa_complementarity.png
    ├── amino_acid_muscle.png
    ├── bcaa_ratio.png
    ├── category_analysis.png
    ├── cost_efficiency_ranking.png
    ├── diaas_comparison.png
    ├── eda_protein_profile.png
    ├── eda_protein_vs_energy.png
    ├── iaa_completeness_heatmap.png
    ├── leucine_threshold.png
    ├── lysine_analysis.png
    ├── muscle_smart_shopper_heatmap.png
    ├── nutrient_profile.png
    ├── quality_vs_cost_scatter.png
    ├── saa_analysis.png
    ├── sensitivity_analysis.png
    └── tryptophan_analysis.png
```    
---

## How to Run

### Requirements
python >= 3.10
pandas
numpy
matplotlib
requests
beautifulsoup4
adjustText
google-auth
google-api-python-client

Install dependencies:
```bash
pip install pandas numpy matplotlib requests beautifulsoup4 adjustText google-auth google-api-python-client
```

### Setup

1. **Download USDA SR Legacy data**
   - Go to [fdc.nal.usda.gov/download-datasets.html](https://fdc.nal.usda.gov/download-datasets.html)
   - Download **SR Legacy** zip file
   - Upload the following files to your Google Drive folder `food_data/sr_legacy_food/`:
     - `food.csv`
     - `food_nutrient.csv`
     - `nutrient.csv`
     - `food_portion.csv`

2. **Open notebooks in Google Colab**
   - All notebooks are designed to run in Google Colab
   - Mount Google Drive when prompted
   - Run notebooks in order: 01 → 02 → 03 → 03b → 04 → 05

3. **Price data**
   - Prices in Notebook 02 are pre-filled from e-Stat June 2026 and personal market observation
   - To update prices, edit the `UNIFIED_PRICES` dictionary in Notebook 02

### Note on Google Drive paths
All notebooks reference `DRIVE_FOLDER_ID = '17Jcona7Ur-iV7miakhLkhHGBgggT0to7'`
Replace this with your own Drive folder ID before running.

---

## Methodology

### Food selection
31 protein sources commonly available in Japanese supermarkets were identified, covering poultry, pork, beef, seafood, dairy, eggs, plant proteins, and supplements. After consolidation of duplicate entries (identical price, DIAAS, and USDA source), the final dataset contains **25 foods**.

### Nutrient data
Nutrient composition (protein, fat, carbohydrates, energy, amino acids) sourced from USDA FoodData Central SR Legacy release. Where a specific Japanese cut had no direct USDA equivalent, the closest available entry was used and documented as a proxy.

### Price data
Prices collected from two sources:
- **e-Stat Retail Price Survey (June 2026)** — national average prices for commodity items
- **Personal market observation (June 2026)** — prices for specific cuts, specialty items, and supplements not available in e-Stat

All prices are tax-inclusive (8% 軽減税率 applies to food items). Prices reflect a single point in time and may vary seasonally, regionally, and by store.

### Protein quality (DIAAS)
DIAAS (Digestible Indispensable Amino Acid Score) scores sourced from FAO/WHO (2013). Where a food-specific score was unavailable, the closest published proxy was used. Scores represent the older child/adult reference pattern.

### Cost efficiency metrics

**Raw cost efficiency:**

yen_per_g_protein = price_jpy_per_100g / protein_g

**Quality-adjusted cost efficiency:**

adjusted_yen_per_g = yen_per_g_protein / (diaas_score / 100)

**Muscle cost efficiency:**

muscle_score = (leucine_g × 0.70) + (diaas_score / 100 × 0.30)
muscle_cost = price_jpy_per_100g / muscle_score

Weights rationale:
- **Leucine 70%** — primary mTORC1 activator and MPS trigger (Norton & Layman, 2006; Churchward-Venne et al., 2012)
- **DIAAS 30%** — overall protein digestibility and IAA completeness (FAO/WHO, 2013)
- BCAA excluded — Spearman correlation with leucine rank = 0.98; adds no independent information

### Amino acid analysis
Individual amino acid profiles extracted from USDA SR Legacy `food_nutrient.csv`. Two foods required special handling:
- **Whey protein** — no AA data in USDA; profile hardcoded from WPC-C aminogram (Gilmour et al., Fonterra)
- **Canned chicken** — no AA data in USDA; scaled from chicken breast AA ratios proportional to protein content (scale factor 1.213)

IAA completeness assessed against FAO/WHO (2013) reference pattern for older child/adult.

---

## Limitations

| Area | Limitation |
|---|---|
| Prices | Single point in time — June 2026. Seasonal variation not captured, especially fresh fish |
| Prices | e-Stat reflects national averages — regional variation exists |
| Pork cuts | All four cuts share one e-Stat proxy price — cut-level differentiation unavailable |
| Beef | Two price points (imported/domestic) across four cuts — CAB-specific pricing unavailable |
| Wagyu | USDA has no wagyu entry — domestic beef loin used as proxy;  |
| White fish | Cod used as proxy for generic white fish — reasonable but approximate |
| Whey protein | WPC-C aminogram used — SAVAS product is WPC; specific aminogram may vary by batch |
| Canned chicken | AA profile scaled from chicken breast — not directly measured |
| Greek yogurt | AA data flagged as potentially underreported (fdc_id 170903) — 7 limiting IAAs inconsistent with dairy literature |
| DIAAS scores | Scores for specific Japanese cuts not published — proxies used instead|
| Supplements | Whey and soy protein included for comparison but not supermarket products — prices from Amazon Japan |

---

## References

- FAO/WHO (2013). *Dietary Protein Quality Evaluation in Human Nutrition*. FAO Food and Nutrition Paper 92.
- Gilmour SR, Holroyd SE, Fuad MD, Elgar D, Fanning AC. *Amino Acid Composition of Dried Bovine Dairy Powders from a Range of Product Streams*. Fonterra Research and Development Centre.
- Norton LE, Layman DK (2006). Leucine regulates translation initiation of protein synthesis in skeletal muscle after exercise. *Journal of Nutrition*, 136(2), 533S-537S.
- Churchward-Venne TA et al. (2012). Supplementation of a suboptimal protein dose with leucine or essential amino acids. *Journal of Physiology*, 590(11), 2751-2765.
- Shimomura Y et al. (2006). Nutraceutical effects of branched-chain amino acids on skeletal muscle. *Journal of Nutrition*, 136(2), 529S-532S.

---

## Author

Built as a portfolio project by Anthony DiSorbo — data analyst based in the greater Tokyo area.
Targeting DA roles in Japan.

[LinkedIn](https://www.linkedin.com/in/adisorbo/) · 
[Tableau Public](https://public.tableau.com/app/profile/anthony.disorbo/vizzes)
