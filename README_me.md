# Aviation Accident Analysis

## Business Problem

A commercial airline/aircraft insurer wants to know which aircraft makes and models exhibit **low rates of total destruction** and **low likelihood of fatal or serious passenger injury** in the event of an accident, plus the operational conditions that most influence accident severity. Recommendations are needed separately for **small aircraft** and **larger passenger aircraft**, and must be backed by statistically robust comparisons.

## Data

- **Source:** NTSB aviation accident data, 1948-2023 (`AviationData.csv`, 88,889 records).
- **Scope:** Filtered to accidents from **1983 onward** (assuming a 40-year aircraft service life), professionally built aircraft only, and fixed-wing airplanes (explicit non-airplane categories such as helicopters and gliders were excluded; unlabeled records were presumed airplanes, consistent with historical NTSB data-entry practice - see `Aviation_Accidents_Cleaning.ipynb` for full reasoning).
- **Final cleaned dataset:** 64,201 records across 81 manufacturers and 5,661 distinct make/model combinations.

## Key Derived Measures

- **`Serious.Fatal.Fraction`** - fraction of occupants aboard who were fatally or seriously injured (fatal + serious injuries divided by total occupants). This normalizes injury outcomes across aircraft of very different sizes.
- **`Destroyed`** - binary flag for whether the aircraft was a total loss.
- **`Size.Class`** - aircraft classified as `Large` if the max occupancy ever recorded for that make/model was 20+ people, otherwise `Small`.

All comparisons between makes/models require a minimum sample size (30+ accidents per make, 10+ per specific model) to keep conclusions statistically meaningful.

## Key Findings & Recommendations

### 1. Aircraft make/model matters - and the effect is large

**Large aircraft:** Modern jet manufacturers - Boeing, McDonnell Douglas, and Airbus - show substantially lower mean fatal/serious injury fractions (7-16%) than general-aviation makes that occasionally appear in the large category (Cessna, Beech, Piper: 30-37%). At the model level, specific types like the **Boeing 777, Boeing 757 variants, and McDonnell Douglas MD-11F/MD-80/MD-88** stand out with injury fractions near 0% across a meaningful accident history.

![Top makes by injury fraction](readme_images/plot_01.png)

**Small aircraft:** Waco, Grumman-Schweizer, Helio, Let, and Maule show the lowest mean injury fractions (10-16%) among small makes with sufficient sample size. At the model level, the **Maule MX-7-235, Grumman-Schweizer G-164A, and various Hiller/Maule models** perform best.

![Destruction rate by make](readme_images/plot_04.png)

**Recommendation:** For large aircraft, favor modern Boeing and McDonnell Douglas jet models. For small aircraft, Maule, Grumman-Schweizer, Hiller, and Enstrom models show the most favorable safety profiles among makes with an established accident history.

### 2. Weather condition is a major factor

Accidents occurring in **Instrument Meteorological Conditions (IMC)** - i.e., poor visibility - have a mean fatal/serious injury fraction of about 66%, nearly **3x** higher than accidents in clear **Visual Meteorological Conditions (VMC)** (about 22%). This difference is highly statistically significant (Welch's t-test, p is approximately 0). Aircraft destruction rates follow the same pattern (59% vs. 17%).

![Injury fraction by weather condition](readme_images/plot_09.png)

**Implication:** Instrument flight capability (avionics + pilot proficiency) is a meaningful, aircraft-independent safety factor worth weighting in risk assessment.

### 3. Phase of flight strongly predicts severity

Accidents during **Taxi** (about 2% injury fraction) and **Landing** (about 4%) are far more survivable than those during **Maneuvering** (about 47%), **Climb** (about 41%), or **Cruise** (about 35%) - phases involving higher speed/altitude and less margin for recovery.

![Injury fraction by phase of flight](readme_images/plot_10.png)

**Implication:** An aircraft's typical mission profile matters. Aircraft used heavily for low-altitude maneuvering work (e.g., aerial application) carry inherently higher risk exposure than aircraft that spend most of their flight time in stable cruise, independent of the airframe's own safety record.

## Repository Contents

- `Aviation_Accidents_Cleaning.ipynb` - data loading, inspection, cleaning, and derived-measure construction; outputs `cleaned_aviation_data.csv`.
- `Aviation_Accidents_Data_Analysis.ipynb` - EDA, make/model safety comparisons, and analysis of weather and phase-of-flight as contributing factors.
- `AviationData.csv` / `USState_Codes.csv` - source data.
- `cleaned_aviation_data.csv` - cleaned/derived dataset used for analysis.

## Caveats

- Injury/destruction fractions describe outcomes **given that a reportable accident occurred** - they do not reflect underlying accident *rates* per flight hour, which this dataset doesn't capture.
- Roughly 13,000 pre-1983 records and non-airplane categories were excluded per the client's scope; results apply specifically to professionally built, fixed-wing aircraft with a plausible active service life.
