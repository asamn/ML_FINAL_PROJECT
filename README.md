
# Predicting U.S. Gun Homicide Rates Using Social Vulnerability Index
## Introduction
This project addresses the problem of predicting gun homicide rates in the United States using socioeconomic and demographic factors represented by the Social Vulnerability Index (SVI). Understanding the factors that contribute to gun violence is crucial for developing effective prevention strategies and resource allocation to mitigate this public health crisis. This project aims to explore the relationship between social vulnerability and gun homicide rates and identify potential predictive features for future analysis and intervention.

## Dataset
The dataset used in this project combines data both from the CDC: gun homicides per 100k capita data and SVI data. The SVI dataset includes indicating features related to socioeconomic status, household characteristics, racial and ethnic minority status, and calculated SVI scores for four themes (represented by the "RPL" flags), based on the indicating features. The SVI dataset reports on a county-level, while gun-homicide is on a state-level. Preporcessing was done to aggregate and group SVI county data into states. All data focuses on the year 2022. 

![screenshot](images/capdata.PNG)

### Structure
The dataset contains 18 features representing various socioeconomic factors, with the target variable being the gun homicide rate per 100,000 population for each state. Features represent estimated percentages of populations falling under a certain socioeconomic criteria, as well as their raw value conterpart. For this project, only percentages will be used to better represent a population. Documentation of these features is provided here: https://www.atsdr.cdc.gov/place-health/media/pdfs/2024/10/SVI2022Documentation.pdf

### Biases
The dataset may contain biases related to data collection and reporting practices. For example, gun homicide data may be underreported in some areas, leading to potential inaccuracies.  Additionally, the homicide rate per 100k varies widely (example: 1.28 in New Hampshire vs. 20.99 in D.C.), suggesting potential skewness and data imbalance.

### Preprocessing
The following preprocessing steps were performed:

-Group county data by states, and performed aggregation via averaging

-Removed irrelevant columns (flags, extraneous state codes, margin of error values)

-Rows with unreliable homicide rates were manually calculated using Execl Formulae

-Only chose features that represented percentage of population rather than raw amounts

-Features starting with "RPL_" were separated as they represent different social vulnerability rankings scores (overall and for individual themes)

-To elimiate the presence of multicolinearity, only the indicating features will be considered for for the models - hence, I omit the social vulerability ranking scores. 

-Data was split between 20% testing and 80% training

### Baseline Performance
Three models were trained and tuned using GridSearchCV to establish a baseline performance metric: Linear Regression, Support Vector Regression (SVR), and XGBoost. The models were evaluated using Mean Squared Error (MSE) and R-squared on a test set. All of the following experiments will use the same parameter grid establshed in the baseline. The results of the baseline training are summarized below:

![screenshot](images/capbase.PNG)

## Experiments
### Scaling Features
Features were scaled using MinMaxScaler. This reduced training times significantly for all models. All models maintained the same MSEs except for SVR, which improved its testing errors and reduced overfitting.

### Adding Features
Polynomial features of 2nd and 3rd order were generated. For 2nd order polynomials, XGBoost performed the best. However, using 3rd order polynomials improved SVR's testing error, making it the best model. XGBoost showed signs of overfitting in this scenario.

### Feature Transformations
PCA was applied to reduce dimensionality, capturing 90% of variance with 6 components. This lowered MSE for SVR and Linear Regression, but raised it for XGBoost.

### Preprocessing
Two variants of the dataset were proposed - one containing the features used to calculate the final SVI values, and the other containing only the SVI values themselves (features with the "_RPF" flags). It was found that the first dataset variant,
the one with the features, worked best for the models.

### Noisy Indicators
Synthetic noise (a random continuous and a random discrete categorical feature) was introduced. This experiment assessed the robustness of the models to irrelevant features. The impact on model performance varied.

## Results and Discussion
![screenshot](images/captimes.PNG)

![screenshot](images/capresult.PNG)
## Recommended Model
Based on the experiment results, the recommended model for predicting U.S. gun homicide rates is SVR with a linear kernel on MinMax scaled features. This model achieved the lowest Test MSE (5.34) compared to other model variants and effectively captured the general pattern in the data without excessive overfitting. This suggests that the dataset appears to have a linear relationship between features and the target variable. 


