# Predicting Northern Anchovy Population Dynamics in the California Current

## 🌊 Project Overview
This project applies machine learning and statistical modeling to untangle the complex, non-linear relationships between marine biogeochemistry and Northern anchovy (*Engraulis mordax*) larvae populations. By synthesizing decades of spatio-temporal oceanographic data, this analysis maps how environmental parameters—such as temperature, dissolved oxygen, and nutrient upwelling—interact to create optimal spawning habitats ("Goldilocks zones").

## 🐟 Background
The Northern anchovy is arguably the important prey fish in the California coast, helping to support massive numbers of predators (whales, seabirds, sea lions, other fish, etc.) and acting as a bridge between plankton and higher trophic levels. As such, tracking the population and health of the species is crucial for keeping the California coast ecosystems, and the State and people of California, healthy and thriving. Currently, this is being done by organizations such as the California Cooperative Oceanic Fisheries Investigations (CalCOFI), who send research ships out to predesignated sites where they then take various recordings and measurements. This includes population counts and densities of marine life such as the Northern anchovy. 

However, these organizations are sometimes not avaliable to take the needed population recordings for a varitey of reasons (funding cuts, ships needing maintanence, geopolitical events, etc.). When that happens, we are left in the dark regarding the well-being of California's marine life, which could lead to disasterous consequences later on, especially if it's for a fish as important as the Northern anchovy.

We also do not have a full understanding of how various climate and environmental variables affect Northern anchovy spawning rates. While prior research has found links between larvae populations and certain factors like upwelling, temperature, etc., a complete grasp is still missing. 

What if we could make decent predictions regarding the larvae population of the Northern anchovy without needing to send out these expeditions? What if we had a tool that could assist the scientists at California Cooperative Oceanic Fisheries Investigations (CalCOFI) in determining if anchovy larvae populations are as they should be? What if we could also find out what climate and environmental features affect Northern anchovy spawning rates? Our project and regressor model aims to solve all these questions.

## 📊 The Data: SIO, NOAA, & ERDDAP
This project heavily relies on gold-standard time-series datasets maintained by the **Scripps Institution of Oceanography (SIO)** and **NOAA**, accessed via the **ERDDAP** data server. The combination of localized bottle sampling and high-resolution satellite imagery provides a uniquely rich, multi-decade feature space for ML applications.

**Datasets Used:**
1. **CalCOFI NOAA Fish Larvae Counts:** Target variable (Anchovy density per 10m²).
2. **CalCOFI NOAA Zooplankton Volume:** Biomass data used to map the base of the predator food web.
3. **CalCOFI SIO Hydrographic Bottle Data:** High-resolution physical and chemical profiles (Salinity, Dissolved $O_2$, Chlorophyll-a, Silicate, Phosphate, Nitrite).
4. **NOAA Optimum Interpolation (OI) SST V2 High Resolution Dataset:** Provided the critical Sea Surface Temperature (SST) feature, leveraging advanced spatial interpolation to map historical climate regimes and currents.

## 🛠️ Data Preprocessing & EDA
Ecological data is notoriously noisy, heavily skewed, and spatio-temporally misaligned. 

- **Spatio-Temporal Grid Mapping:** Continuous latitude/longitude coordinates were quantized into localized 0.25-degree geographic bins. Time series were discretized into 4 distinct meteorological seasons to match CalCOFI's quarterly cruise schedules.
- **Depth Filtering:** Because anchovy larvae are epipelagic, hydrographic bottle data (which spans thousands of meters) was strictly filtered to the top 10 meters to represent accurate sea-surface conditions.
- **Target Normalization:** Larval catch counts follow a highly exponential, zero-inflated distribution. A $log(x)$ transformation was applied to the target variable (`log_larvae`) and extreme right-skewed nutrient metrics to stabilize model variance.
- **Geospatial Animation:** Conducted frame-by-frame rendering of historical SST mapping overlaid with log-scaled catch densities to visually validate historical regime shifts.

## 🧠 Feature Engineering
- **Handling Multicollinearity:** Ocean physics dictate that cold, deep upwelled water is inherently nutrient-rich, high in $O_2$, and highly saline. This creates massive multicollinearity traps for linear models. 
- **Temporal Lags (The "Pantry" Metric):** Cross-sectional modeling showed Zooplankton to be statistically insignificant when matched with current-day Anchovy populations (the "they already ate the food" dilemma). We engineered a **T-1 Season (3-month) time lag** on zooplankton volume. By transforming it into a historical leading indicator, its predictive importance skyrocketed.

## 🤖 Modeling Strategy
### 1. Ordinary Least Squares (OLS) Regression
Used primarily as a diagnostic baseline. 
- **Findings:** Demonstrated the mathematical limitations of linear models in ecology ($R^2  pprox 0.107$). OLS correctly identified 'Season' and 'Salinity' as statistically significant ($p < 0.05$), but collapsed under the weight of multicollinearity when interacting biological variables (Chlorophyll, Phosphates, Silicates) were introduced, failing to detect threshold-based biological carrying capacities.

### 2. Random Forest Regressor
Selected to map the complex, non-linear interactions and threshold dynamics ("Goldilocks zones") without suffering from multicollinearity penalty. 
- **Performance:** Achieved an $R^2  pprox 0.236$ (a ~400% improvement over initial linear baselines), a highly respectable variance capture for uncontained, wild ecological systems.
- **Feature Importance Hierarchy:** 1. **Sea Surface Temperature (SST):** Emerged as the undisputed macro-proxy for ocean currents and climate regimes once underlying chemical data was controlled for.
  2. **Dissolved $O_2$ & Salinity:** Confirmed as the primary localized physical drivers for larval survival.
  3. **Lagged Zooplankton:** Proved the importance of historical cause-and-effect in ecological time series.
  4. **The "Upwelling Cocktail":** Chlorophyll-a, Silicate, and Phosphate grouped together to successfully identify localized nutrient upwelling events.

## 🚀 Future Scope
To push the variance capture even higher, future iterations of this model will integrate macro-climate indices (Pacific Decadal Oscillation, El Niño Southern Oscillation) to account for decadal regime shifts, alongside static geological features like coastal bathymetry to delineate open-ocean vs. shelf spawning habitats.