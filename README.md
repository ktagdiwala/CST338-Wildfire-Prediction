## **Introduction (10%)**

Wildfires in the U.S. are becoming more frequent and severe, putting communities and resources under strain. In this project, we explore correlations between wildfire features such as their location, discovery date, size, and cause. The data used to train and test our prediction models is obtained from the 1.88 Million US Wildfires dataset (1992–2015).

The purpose of this research is to:

- **Predict the cause of a given fire (human-made or natural)**. This can help authorities identify potential responsible parties, prevent future incidents, and improve public safety initiatives.  
- **Estimate the size of the given fire.** This allows the authorities to determine containment strategies and the amount of resources to allocate.  
- **Classify whether or not a given fire will grow past 100 acres.** In addition to containment strategies and resource allocation, the identification of large fires helps the authorities prioritize response time, public communication, and emergency evacuations.

## **Selection of Data (20%)**

* **Dataset Source**: “1.88 Million US Wildfires (1992–2015)” from Kaggle, originally from the Forest Service Fire Program Analysis (FPA FOD) database [https://www.kaggle.com/datasets/rtatman/188-million-us-wildfires](https://www.kaggle.com/datasets/rtatman/188-million-us-wildfires)  
* **Key Fields**: DISCOVERY\_DATE, CONT\_DATE, LATITUDE, LONGITUDE, FIRE\_SIZE, FIRE\_SIZE\_CLASS, STAT\_CAUSE\_DESCR, etc.  
* **Cleaning & Feature Engineering**:  
  * Remove records outside of the contiguous US  
  * Drop features with ≥60% missing data  
  * Derive MONTH, YEAR from discovery date  
  * Define cont\_days \= CONT\_DATE \- DISCOVERY\_DATE  
  * Encode CAUSE\_HUMAN (binary)  
  * Define Large\_Fire \= FIRE\_SIZE \> 100 acres  
  * Log-transform FIRE\_SIZE for regression tasks.

## **Methods (20%)**

* **Binary Classification (Large vs. Small fire)**:  
  * Baseline: Logistic Regression  
  * Main: RandomForestClassifier (n\_estimators≈20). Chosen for non-linear interactions, robustness to mixed types, and feature importance output.  
  * Imbalance: Initial evaluation on unbalanced split; note need for future resampling or class-weight adjustments.  
* **Regression (Predicting Size)**:  
  * Target: log1p(FIRE\_SIZE).  
  * Model: RandomForestRegressor. Preferred over Linear Regression due to non-linear effects.  
* **Binary Classification (Man-made vs natural)**:  
  * Target: CAUSE\_HUMAN (determine if a fire is man made or natural)  
  * Model: RandomForestClassifier  (n\_estimators≈20). Chosen for handling non-linear interactions, mixed data types, and built-in class imbalance support  
  * Imbalance Handling: Used class\_weight='balanced' due to a \~60/40 class split favoring human-caused fires.  
* **Evaluation Metrics**:  
  * Classification: Accuracy, Precision, Recall, F1-score  
  * Regression: RMSE on original scale after inverse transform, R² on log scale.  
* **Justification**: Tree-based models can capture nonlinear relationships and naturally handle datasets with missing values without extensive imputation. Random Forests also offer interpretability through feature importance scores, while simpler models serve as useful baselines to gauge whether more complex methods provide real gains.

## **Results (20%)**

* **Classification** (using features like LATITUDE, LONGITUDE, MONTH, CAUSE\_HUMAN):  
  * **Accuracy**: \~97% (driven by majority small fires).  
  * **Large-fire Recall**: \~0.08 (very low), Precision \~0.33; F1 \~0.13.  
  * **Interpretation**: Model rarely flags large fires, indicating basic spatiotemporal features insufficient to capture rare event drivers.  
* **Regression**:  
  * **RMSE**: \~1800 acres (large relative to 100-acre threshold).  
  * **Interpretation**: High error reflects skewed distribution and missing predictors  
* **Feature Importance**: Month and cause often rank high, but alone cannot discriminate against extreme spread potential.  
* **Classification (Man-made vs. Natural Fire):**  
  * **Accuracy:** 71.8%  
  * **Precision / Recall / F1:**  
    * **Human-made:** 0.76 / 0.77 / 0.76  
    * **Natural:** 0.65 / 0.65 / 0.65  
  * **Interpretation:** Performs well for human-caused fires, natural fires harder to distinguish.

## **Discussion (20%)**

* **Implications**: Baseline models highlight that limited features yield poor detection of large fires despite high overall accuracy. Early-warning requires richer, dynamic inputs such as weather data, fuel moisture measurements, and vegetation conditions.  
* **Comparison with other research**: Our results and other research work show that relying solely on historical patterns limits predictive insights. Research must integrate real-time climate indicators and richer environmental datasets. Research must also test how these dynamic inputs improve model performance.  
  [https://greghcam.medium.com/analyzing-25-years-of-wildfire-data-in-the-us-b3dd4ef7ec26](https://greghcam.medium.com/analyzing-25-years-of-wildfire-data-in-the-us-b3dd4ef7ec26)  
* **Future Work**:  
  * Integrate weather (wind, humidity), drought indices, satellite-derived fuel metrics  
  * Validate on more recent years and perform backtesting on major fire seasons

## **Summary (10%)**

We built three models using basic wildfire record data (like time, location, and cause):

1. Predict if a fire would be **large** (\>100 acres)  
2. Predict the **potential size** of a fire  
3. Predict whether a fire was **human-caused**

These models gave us a strong starting point. We saw high overall accuracy and learned that while simple inputs catch broad patterns like seasonality and common causes, they struggle with harder tasks like spotting rare, extreme fires. Predicting fire size or cause accurately will take more detailed data, like weather, fuel conditions, and vegetation info.
