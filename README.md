# Aviation Accident Analysis

An exploratory data analysis of aviation accident data (1948-2022) for a fictional client, an airline/airplane insurer. The client wants to know which **airplane makes and models** have low rates of total destruction and a low likelihood of fatal or serious injury to occupants, and which general conditions drive those outcomes.

## Business questions

1. Which makes/models should the client consider, **separately for small aircraft and large passenger aircraft**?
2. Which factors (e.g. weather, engines, phase of flight) are associated with serious injury or aircraft destruction?

The client only cares about **professionally built airplanes** that could still be active, so events before **1983** are excluded (assuming a maximum 40-year type lifetime).

## Repository structure

```
.
├── Aviation_Accidents_Cleaning.ipynb       # Part 1: load, inspect, clean, build measures, save CSV
├── Aviation_Accidents_Data_Analysis.ipynb  # Part 2: EDA, statistics, recommendations
├── data/
│   ├── AviationData.csv                    # raw NTSB aviation accident data
│   ├── AviationData_cleaned.csv            # output of the cleaning notebook
│   └── USState_Codes.csv                   # US state abbreviations (not needed for this analysis)
└── README.md
```

## How to run

1. Install Python 3 with `pandas`, `numpy`, `matplotlib`, `seaborn` and `scipy`.
2. Run **`Aviation_Accidents_Cleaning.ipynb`** first. It reads `data/AviationData.csv` and writes `data/AviationData_cleaned.csv`.
3. Run **`Aviation_Accidents_Data_Analysis.ipynb`**, which loads the cleaned file.

Keep the notebooks in the project root so the `data/` paths resolve.

## Data

NTSB aviation accident and incident records: one row per aircraft involved in an event (88,889 raw rows, 31 columns). Over 90% of the events are in the United States. The raw file needs `encoding="latin-1"` to load.

## Approach

**Cleaning (notebook 1)**
- Imputed the missing `Aircraft.Category` (about 64% missing) from the most common category of the same Make + Model, then kept professionally built airplanes from 1983 onward.
- Treated missing injury counts as zero (confirmed: they occur only on non-fatal events) and dropped rows with no occupant information.
- Built the key measures:
  - `Total.Passengers`: total occupants, including crew
  - `Fatal.Serious.Fraction`: fatally or seriously injured occupants divided by total occupants
  - `Destroyed`: 1 if the aircraft was destroyed, otherwise 0
- Standardised `Make` (1,343 raw names reduced to 49 makes with at least 50 events), created a unique `Make.Model` identifier, and cleaned engine, weather, purpose and phase-of-flight columns.
- Dropped mostly empty columns and saved the result: **57,468 rows x 34 columns**.
- Repeated `Event.Id` values are different aircraft in the same event (for example mid-air collisions), not duplicates, so they were kept.

**Analysis (notebook 2)**
- Classified each plane type as **small or large** using the 90th percentile of occupants against a threshold of 20. The maximum was rejected because data errors distort it (for example a Cessna 172M with 124 occupants recorded).
- Compared makes and models on injury fraction and destroyed rate, with 95% confidence intervals and minimum sample sizes (30 events per make, 10 per model).
- Ran robustness checks: an accidents-only comparison for large aircraft, and a hold-out test for the model rankings.
- Analysed three factors with statistical tests: weather condition, number of engines and phase of flight.

## Key findings

**Small aircraft**
- Cessna (n = 25,100) is modestly but significantly better than the small-aircraft average on both measures.
- Maule, Aviat and Diamond have the lowest destroyed rates among makes with adequate samples.
- Beech, Mooney and Cirrus show above-average severity.
- Vintage and agricultural types (Waco, Great Lakes, Stearman, Ag-Cat) look good on injury fraction but are niche, small-sample categories.

**Large aircraft**
- Bombardier, Embraer, McDonnell Douglas, Boeing and Airbus are the lowest-risk makes. The ranking holds when only accidents are kept.
- De Havilland, Douglas and Beech (older commuter and early jet types) perform worse.
- Individual model rankings are statistically fragile (10 to 24 events each), so recommendations are made at the family level.

**Contributing factors**
1. **Weather:** events in instrument conditions (IMC) have about 3x the injury fraction (0.659 vs 0.219) and about 3.6x the destroyed rate (58.7% vs 16.3%) of events in visual conditions.
2. **Phase of flight:** for small aircraft, maneuvering and climb are the most severe phases; landing and taxi are the least severe.
3. **Number of engines:** twin-engine small aircraft do worse overall, mainly because they fly in IMC more often and are used more for business flying. They do better than singles in IMC. Engine count is not a protective factor on its own.

## Limitations

- The data contains events, not flights. Without flight hours, the analysis measures severity *given an event*, not the probability of having one.
- `Total.Passengers` includes crew.
- Model variants (for example 172M and 172N) are treated as separate plane types, which reduces sample sizes.
- Results are observational. Weather, mission type and aircraft type are intertwined, so they show association, not causation.

## Tools

Python, pandas, NumPy, Matplotlib, Seaborn, SciPy, Jupyter.

## Author

Emmanuel Nyakoe Omare - [github.com/Manu254P](https://github.com/Manu254P)
