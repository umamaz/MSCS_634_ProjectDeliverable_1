# Telco Customer Churn

**Project Deliverable 3 · Classification, Clustering, and Pattern Mining**

MSCS-634-M50 · Advanced Big Data and Data Mining

Group project · Federico Poi and Umama Azhar

## Dataset and goal

We used the same [IBM Telco Customer Churn dataset](https://github.com/IBM/telco-customer-churn-on-icp4d/blob/master/data/Telco-Customer-Churn.csv) as in Deliverables 1 and 2. It contains 7,043 customers and 21 original columns. We predicted whether a customer left, grouped customers with similar accounts, and looked for combinations of attributes associated with churn.

## Classification

We compared k-Nearest Neighbors (k-NN) and a Decision Tree on an 80/20 stratified split. We fixed k-NN at 21 neighbors with equal votes and used balanced class weights for the tree. Five-fold grid search picked a maximum tree depth of 7 and a minimum of 50 customers per leaf. Tuning raised the tree's mean validation F1 from 0.4987 to 0.6226. The fixed k-NN model had a mean validation F1 of 0.5936 on the same folds.

| Model | Held-out accuracy | Held-out churn F1 | Held-out churn recall | Held-out ROC AUC |
| --- | ---: | ---: | ---: | ---: |
| k-NN | 0.7828 | 0.5887 | 0.5856 | 0.8240 |
| Tuned Decision Tree | 0.7509 | 0.6254 | 0.7834 | 0.8370 |

k-NN correctly classified more customers overall. The tuned tree caught more customers who churned and had higher churn F1 and ROC AUC. We would start with the tuned tree for outreach and use the confusion matrix to weigh how many extra customers we would contact.

## Customer groups

We used K-Means with three clusters based on tenure, monthly charges, contract type, and internet service. We used churn only to describe the groups afterward. The sampled silhouette score was 0.356.

| Group | Customers | Observed churn |
| --- | ---: | ---: |
| Mostly no internet and low monthly bills | 1,705 | 7.9% |
| Short tenure and mostly month-to-month contracts | 3,006 | 46.9% |
| Long tenure and higher monthly bills | 2,332 | 13.9% |

The short-tenure group is where we would start testing a welcome check-in or service review.

## Association rules

Apriori found 187 frequent itemsets and 10 churn rules after filtering by support, confidence, lift, and redundancy. The highest-lift rule combined a month-to-month contract with fiber optic service. It had **16.5% support**, **54.6% confidence**, and **2.06 lift**. We would use the rule to choose an audience for a retention test and compare that group with customers receiving usual support. The rule shows an association in this sample.

## Challenges and decisions

- **Blank cumulative charges.** We converted 11 whitespace-only entries to missing values and kept all 7,043 customers. None of the three analyses uses cumulative charges.
- **Imbalanced churn.** We stratified the split and validation folds, weighted classes in the tree, and evaluated F1 and ROC AUC alongside accuracy.
- **Mixed feature types and repetitive rules.** We scaled numeric features and encoded categories for clustering. In rule mining, we removed longer rules when a shorter rule had at least the same confidence.

## Run the notebook

Open [MSCS_634_ProjectDeliverable_3.ipynb](MSCS_634_ProjectDeliverable_3.ipynb) in Jupyter or Google Colab and run the cells in order. The notebook downloads the public CSV and installs mlxtend if needed. It includes the confusion matrices, ROC curves, cluster profiles, and a chart of the highest-lift rules.
