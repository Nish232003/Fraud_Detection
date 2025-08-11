# Proactive Fraud Detection with Machine Learning

## Introduction

This project aims to proactively detect fraudulent financial transactions using a machine learning model. The model analyzes a large dataset of financial transactions to identify key patterns and factors that indicate fraud. Based on these insights, this project provides actionable recommendations for updating a company's fraud prevention infrastructure, complete with a strategy for measuring the success of the implemented changes.

## Table of Contents
- [Project Objective](#project-objective)
- [Dataset](#dataset)
- [Methodology](#methodology)
  - [1. Data Cleaning](#1-data-cleaning)
  - [2. Feature Engineering](#2-feature-engineering)
  - [3. Handling Class Imbalance (SMOTE)](#3-handling-class-imbalance-smote)
  - [4. Model Training & Evaluation](#4-model-training--evaluation)
- [Key Findings](#key-findings)
- [Actionable Recommendations](#actionable-recommendations)
- [How to Measure Success](#how-to-measure-success)
- [How to Run this Project](#how-to-run-this-project)
- [Results](#results)

## Project Objective

The primary goal is to develop a machine learning model to distinguish between legitimate and fraudulent transactions. The insights from the model are then used to create a set of actionable, data-driven recommendations to enhance fraud prevention systems.

## Dataset

The dataset used is a synthetic financial dataset from Kaggle that mimics real-world transaction data.

**Features:**
- **step**: Represents a unit of time in the real world. In this case, 1 step is 1 hour of time.
- **type**: The type of transaction (e.g., `CASH_OUT`, `PAYMENT`, `TRANSFER`).
- **amount**: The amount of the transaction in the local currency.
- **nameOrig**: The customer who initiated the transaction.
- **oldbalanceOrg**: The initial balance of the originator's account before the transaction.
- **newbalanceOrig**: The new balance of the originator's account after the transaction.
- **nameDest**: The customer who is the recipient of the transaction.
- **oldbalanceDest**: The initial balance of the recipient's account before the transaction.
- **newbalanceDest**: The new balance of the recipient's account after the transaction.
- **isFraud**: This is the target variable. It is `1` if the transaction is fraudulent and `0` otherwise.
- **isFlaggedFraud**: An indicator from the system that flags a transaction as suspicious.

## Methodology

The project follows a structured machine learning workflow to ensure the model is robust and the findings are reliable.

### 1. Data Cleaning
- **Handled Outliers**: Extreme values in the `amount` column were capped using the Interquartile Range (IQR) method to prevent them from skewing the model. The transaction amounts were clipped to a range of **$0.10 to $516,181.13**.
- **Removed Unnecessary Columns**: The `nameOrig` and `nameDest` columns were dropped as they are unique identifiers and do not provide predictive value for a general fraud detection model.

### 2. Feature Engineering
- **One-Hot Encoding**: The categorical `type` column was converted into a numerical format using one-hot encoding. This created a new binary column for each transaction type, allowing the model to interpret this information.

### 3. Handling Class Imbalance (SMOTE)
- **Problem**: The dataset is highly imbalanced, with fraudulent transactions making up only **0.11%** of the data. This can bias the model towards predicting non-fraudulent transactions.
- **Solution**: **SMOTE (Synthetic Minority Oversampling Technique)** was applied to the *training data only*. SMOTE creates synthetic fraudulent examples to balance the classes, allowing the model to learn the patterns of fraud more effectively without overfitting.

### 4. Model Training & Evaluation
- A **Logistic Regression** model was chosen as a baseline for its simplicity and interpretability.
- The model was trained on the **balanced (SMOTE-adjusted) training data**.
- Its performance was evaluated on the **original, imbalanced test set** to simulate real-world conditions.

## Key Findings

The logistic regression model identified several key factors that strongly predict whether a transaction is fraudulent.

- **Strongest Fraud Indicators (Positive Correlation)**:
  - `type_TRANSFER`: Transactions involving a transfer of funds.
  - `type_CASH_OUT`: Transactions involving a cash withdrawal.

- **Strongest Legitimate Indicators (Negative Correlation)**:
  - `type_PAYMENT`: Routine payment transactions are highly unlikely to be fraudulent.

The existing `isFlaggedFraud` system flag had a neutral impact, suggesting it is not effective in identifying the types of fraud present in this dataset.

![Feature Importance](https://i.imgur.com/G5gE3Vv.png)

## Actionable Recommendations

Based on the model's findings, the following prevention strategies are recommended:

1.  **Implement Mandatory 2FA for High-Risk Transactions**: For all transactions of type `TRANSFER` and `CASH_OUT`, trigger a mandatory Two-Factor Authentication (2FA) step before the transaction is processed.
2.  **Create a Tiered Alert System**: Since `PAYMENT` transactions are overwhelmingly safe, they can be processed with lower scrutiny. This allows fraud prevention resources to be focused on the high-risk `TRANSFER` and `CASH_OUT` transaction types, improving efficiency.

## How to Measure Success

To determine if these new measures are effective, the following plan should be implemented:

- **A/B Testing**: Roll out the new 2FA requirement to a **test group (50% of users)** while the remaining **control group (50%)** continues with the current system.
- **Key Metrics**: Over a 3-month period, monitor:
    - The fraud rate in both groups.
    - The false positive rate (how many legitimate transactions are incorrectly flagged).
    - The transaction completion rate to ensure legitimate customers are not overly inconvenienced.
- **Definition of Success**: The new measures will be considered successful if the test group shows a statistically significant reduction in the fraud rate without a major negative impact on the transaction completion rate.

## How to Run this Project

To reproduce this analysis, follow these steps:

**1. Prerequisites**
Ensure you have Python 3 and the following libraries installed:
- `pandas`
- `numpy`
- `scikit-learn`
- `matplotlib`
- `seaborn`
- `imbalanced-learn`

**2. Installation**
You can install the required libraries using pip:
```bash
pip install pandas numpy scikit-learn matplotlib seaborn imbalanced-learn
