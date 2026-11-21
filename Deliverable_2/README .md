# Telco Customer Churn

**Residency Day 2 · Project Deliverable 2**  
**Regression Modeling and Performance Evaluation**  
MSCS-634-M50 · Advanced Big Data and Data Mining  
Group project · Federico Poi and Umama Azhar

## Dataset and goal

We used the [IBM Telco Customer Churn dataset](https://github.com/IBM/telco-customer-churn-on-icp4d/blob/master/data/Telco-Customer-Churn.csv), which has 7,043 customers and 21 columns. We predicted `MonthlyCharges`, the customer's monthly bill in dollars. In Deliverable 1, we saw that bills varied by internet service and phone setup. This time we tested how well those service details could predict the bill.

## What we tried

We built Multiple Linear Regression and Ridge (`alpha=1.0`) and compared four feature sets: the 17 original predictors, those predictors plus an add-on count, both engineered features, and nine service features only. The second engineered feature combined fiber optic service with the add-on count so the models could estimate a different add-on effect for fiber customers.

We used the same five validation folds for every comparison and ranked the configurations by mean RMSE. The models' pipelines learned the numeric scaling and categorical encoding from each fold's training rows. We then refit the best configuration for each model on all 5,634 training customers and evaluated both on the same 1,409 test customers.

## Results

| Model with nine service features | Mean CV RMSE | Test RMSE | Test MSE | Test R² |
| --- | ---: | ---: | ---: | ---: |
| Multiple Linear Regression | 1.0203 | 1.0504 | 1.1034 | 0.9988 |
| Ridge | 1.0203 | 1.0506 | 1.1038 | 0.9988 |

RMSE is in dollars and MSE is in dollars squared. Multiple Linear Regression with nine service features had the lowest validation RMSE before rounding. On the test set, the two models were separated by roughly two hundredths of a cent in RMSE. We chose Multiple Linear Regression for its simpler setup.

The add-on count made little difference. Adding the fiber interaction slightly increased validation RMSE for both models. The nine service columns kept prediction error close to a dollar and performed about as well as the full set of predictors. That makes sense for a bill tied to a known service bundle. The scores describe this dataset and pricing structure.

## Challenges and decisions

- **Billing information in the inputs.** We excluded `TotalCharges` because past bills could indirectly reveal the amount we were trying to predict. This also let us keep the 11 customers with blank cumulative charges.
- **Different labels for unavailable services.** For the add-on count, we counted only `Yes`. We kept the original service labels for one-hot encoding and configured the encoder to handle categories it had not seen during training.
- **Nearly identical scores.** We compared models on identical folds, reported fold-to-fold variation, and chose the smaller feature set because the accuracy was practically the same.

## Run the notebook

Open [MSCS_634_ProjectDeliverable_2.ipynb](MSCS_634_ProjectDeliverable_2.ipynb) in Jupyter or Google Colab and run the cells from top to bottom. The notebook downloads the CSV and includes the validation tables, test scores, and comparison charts. It uses Python with pandas, scikit-learn, and Matplotlib.
