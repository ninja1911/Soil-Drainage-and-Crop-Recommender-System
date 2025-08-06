# Soil-Drainage & Crop Recommender System ( **swatML** )

A full-stack geospatial + machine-learning pipeline that **predicts field-level flood or drainage risk** and then **recommends the best-fit crops** and management practices for every parcel on earth.  
The project fuses *Harmonized World Soil Database* raster layers, *NOAA* daily weather records, and open crop-yield statistics to create actionable, location-specific insights for farmers, agronomists, and watershed managers.

---

## 🌱 Why It Matters
- Drainage drives yield—waterlogged soils suffocate roots, while overly free-draining soils leach nutrients.  
- Traditional on-site infiltration tests are slow and costly; satellite + ML brings near-instant diagnostics.  
- Linking drainage risk with **crop-specific tolerances** lets growers plant the *right* cultivar in the *right* field, cutting input waste and boosting resilience to extreme weather.

---

## 🛰️ Data Sources
| Layer | Source | Resolution / Records | Key Variables |
|-------|--------|----------------------|---------------|
| **Soil** | HWSD raster | 662 961 grid-cells × 59 columns | texture, organic C, pH, drainage class |
| **Climate** | NOAA GHCN-Daily | ~100 000 stations, 1980-2025 | 15-day aggregates of precipitation & snowfall |
| **Crops** | National/FAO stats | county-/district-level | crop type, yield, area |

---

## 🧩 Data Integration Pipeline
1. **Raster → Points**   
   Convert each HWSD pixel to *(lat, lon, MU_GLOBAL)* via affine transforms.  
2. **Nearest-Station Join**   
   Match every soil point to its closest NOAA weather station using a KD-Tree spatial index.  
3. **15-Day Climate Roll-ups**   
   Roll daily precipitation/snow into semi-monthly totals for smoother trends.  
4. **Master Table Build**   
   Merge soil, climate, and crop records into a unified feature set with > 650 K rows.  

Data quality steps include outlier pruning, region-aware mode/median imputation, and SMOTE oversampling for rare classes.

---

## 🛠️ Feature Engineering
* Season & month encodings from date stamps  
* Synthetic **Flood Risk** label derived from soil‐drainage class to enable supervised training  
* Agro-climatic indices: total growing-season rainfall, snow-melt water, temperature degree-days  
* Soil-texture fractions (% sand/silt/clay) and fertility proxies (organic C, CEC)  

---

## 🤖 Modelling Strategy

### 1. Flood / Drainage-Risk Classifier  
| Algorithm | Accuracy | Precision | Recall |
|-----------|----------|-----------|--------|
| Logistic Regression | 0.90 | 0.90 | 0.89 |
| **Random Forest** | **0.92** | **0.93** | **0.92** |
| SGD Classifier | 0.87 | 0.88 | 0.87 |

Random Forest won on both F1 and robustness.

### 2. Crop Suitability Recommender  
Gradient-Boosting trees (XGB-Rank) yielded **87 % accuracy** in predicting optimal crop labels, outperforming RF and LR.

**Technical novelties**

* Spatial KD-Tree matching of soil pixels to climate stations  
* 15-day climate aggregation for noise-reduced trends  
* Synthetic flood labels from drainage to create a hard target  
* Imbalance handling with SMOTE for rare flood events and specialty crops

---

## 📈 Results & Insights
* **Macro F1 = 0.83** on drainage prediction; top-3 NDCG = 0.93 for crop ranking.  
* High-risk flood zones map cleanly onto poorly drained clay-loam belts (see `reports/figures/flood_hotspots.png`).  
* Chickpea, lentil, and sorghum dominate well- to excessively-drained soils, whereas rice and taro excel in poorly drained basins—mirroring agronomic guidelines.

---

## 🛤️ Road-Map

* 🌾 Integrate soil-pH & nutrient maps for fertiliser advice
* 🔎 Publish ONNX model zoo for edge inference on drones
* ☁️ Deploy serverless API via AWS Lambda + S3 tile cache
* 📄 Submit methodology pre-print to *Computers & Geosciences*

---

## 🤝 Contributing

Pull requests are welcome—please open an issue first to discuss changes and run `pre-commit` before pushing.

---

## 📜 License

Released under the **MIT License**.

---

### Acknowledgements

Thanks to **USDA NRCS**, **ESA Copernicus**, **NOAA** and every open-source maintainer who made this work possible.

> “If you don’t own the data, you can’t own the farm.”

