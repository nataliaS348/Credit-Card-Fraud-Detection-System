# CEN 352 – Artificial Intelligence

## Project Report

### Title: Credit Card Fraud Detection
### Group: Natalia Simaku, Lea Lila, Vanesa Erindi


## 1. Introduction

The rapid growth of digital payment systems has significantly increased the volume of credit card transactions worldwide. While this shift has improved convenience and accessibility, it has also led to a rise in fraudulent activities, causing substantial financial losses for banks and consumers. Credit card fraud detection has therefore become a critical problem in the financial sector, requiring intelligent and automated solutions capable of identifying fraudulent transactions in real time.

Traditional rule-based systems struggle to adapt to evolving fraud patterns, as fraudsters continuously modify their strategies. Artificial Intelligence (AI) offers a powerful alternative by learning hidden patterns from historical transaction data and adapting to new fraud behaviors. This project focuses on designing an intelligent agent capable of classifying credit card transactions as **fraudulent** or **legitimate**, using multiple AI techniques.

The goal of this project is to develop and evaluate a **hybrid AI-based fraud detection system** that combines:

* A **machine learning classifier** for predictive modeling.
* An **anomaly detection technique** to identify suspicious and rare transaction patterns.

By integrating these approaches, the system aims to improve detection performance, particularly for highly imbalanced datasets where fraudulent transactions represent a very small fraction of the data.


## 2. Methodology

This project follows a **supervised learning approach combined with unsupervised anomaly detection**, forming an intelligent agent that learns from historical data and flags suspicious behavior.

### AI Techniques Used

1. **Supervised Machine Learning** – XGBoost is used to classify transactions as fraudulent or legitimate based on labeled historical data.
2. **Unsupervised Learning (Deep Learning)** – An Autoencoder neural network is used to identify anomalous transactions without relying on class labels.

### Agent Perspective

**Performance Measure**: Recall, F1-score, LogLoss

**Environment**: Financial transaction system

**Actuators**: Fraud decision (Fraud / Legitimate / Suspicious)

**Sensors**: Transaction features such as amount, time, and anonymized PCA components


## 3. Dataset and Data Processing

### 3.1 Dataset Description

The project uses the **Credit Card Fraud Detection Dataset** made publicly available on [Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud?resource=download), originally 
collected by European cardholders.

The dataset contains transactions made by credit cards in September 2013 by European cardholders.
This dataset presents transactions that occurred in two days, where we have 492 frauds out of 284,807 transactions. The dataset is highly unbalanced, the positive class (frauds) account for 0.172% of all transactions.

It contains only numerical input variables which are the result of a PCA transformation. Unfortunately, due to confidentiality issues, we cannot provide the original features and more 
background information about the data. Features V1, V2, … V28 are the principal components obtained with PCA, the only features which have not been transformed with PCA are 'Time' and 'Amount'. 
Feature 'Time' contains the seconds elapsed between each transaction and the first transaction in the dataset. The feature 'Amount' is the transaction Amount, this feature can be used for 
example-dependant cost-sensitive learning. Feature 'Class' is the response variable and it takes value 1 in case of fraud and 0 otherwise.

### 3.2 Data Preprocessing

As we said before the dataset is highly unbalanced, making fraud detection a challenging task. Therefore the following preprocessing steps were applied:

1. **Data Cleaning**

   * Verified absence of missing values.
   * Ensured numerical consistency across all features.

2. **Feature Scaling**

   * `Amount` and `Time` were normalized using **StandardScaler** to ensure equal contribution during training.

3. **Handling Class Imbalance**

   * **SMOTE (Synthetic Minority Oversampling Technique)** was applied to balance the dataset during supervised model training.
   * Original imbalanced data was retained for anomaly detection.

4. **Train-Test Split**

   * 70% training data
   * 30% testing data
   * Stratified split to preserve class distribution.

These preprocessing steps ensure fair evaluation and stable model performance.


## 4. Models

### 4.1 XGBoost Classifier (Supervised Learning)

XGBoost (Extreme Gradient Boosting) was selected as the primary supervised learning model due to its strong performance on structured, imbalanced datasets. The algorithm builds decision trees sequentially, where each new tree focuses on correcting errors made by previous trees.

**Model configuration:**
- Objective function: Binary logistic regression
- Evaluation metric: LOGLOSS
- Class imbalance handled using StandardScaler
- Hyperparameters tuned:
    - Maximum tree depth
    - Number of estimators
    
      
**Advantages:**
- High predictive accuracy
- Built-in regularization
- Effective handling of non-linear relationships

XGBoost serves as the main classification engine of the agent, detecting fraud based on learned historical patterns.

### 4.2 Autoencoder Neural Network (Unsupervised Learning)

An Autoencoder neural network was used for anomaly detection. Unlike supervised models, the Autoencoder does not rely on labeled fraud data. 
Instead, it learns the normal behavior of legitimate transactions.

**Architecture:**
- Input layer matching feature dimensions
- Encoder layers that compress the data
- Bottleneck layer
- Decoder layers that reconstruct the input
- Loss function: Mean Squared Error (MSE)
  
**Training strategy:**
- Trained only on legitimate transactions
- High reconstruction error indicates anomalous behavior
- Role in the system:
  
The Autoencoder detects novel or rare transaction patterns, helping identify fraud cases that may not be captured by supervised learning alone.

### 4.3 Hybrid Fraud Detection Strategy

The final decision is made by combining the outputs of XGBoost and the Autoencoder:

| XGBoost Prediction | Autoencoder Output | Final Decision             |
| ------------------ | ------------------ | -------------------------- |
| Fraud              | Anomaly            | High-risk fraud            |
| Fraud              | Normal             | Fraud                      |
| Normal             | Anomaly            | Suspicious (manual review) |
| Normal             | Normal             | Legitimate                 |

For each incoming transaction, predictions are generated independently by both the XGBoost classifier and the autoencoder anomaly detector. The XGBoost model predicts based on learned patterns from labeled data, while the Autoencoder computes a reconstruction error to determine whether the transaction is anomalous. The outputs of both models are then combined using a rule-based decision engine to produce the final risk classification.

## 5. Evaluation Metrics

Given the class imbalance, accuracy alone is not sufficient. The following metrics were used:

* **Mean Squared Error (MSE) Loss** – Measures the average squared difference between predicted probabilities and actual labels, penalizing larger prediction errors
* **Recall** – Measures ability to detect fraud cases
* **F1-score** – Balance between precision and recall
* **LogLoss** – Overall model discrimination capability


## 6. Ethical and Societal Considerations

AI-based fraud detection systems must balance security with fairness. False positives can inconvenience customers and reduce trust. To address this, 
the system flags borderline cases for manual review rather than automatically blocking transactions. Additionally, all features are anonymized, 
reducing privacy risks and bias..

## 7. Conclusion

This project demonstrates that combining XGBoost with an Autoencoder neural network creates a powerful and flexible fraud detection agent. The hybrid approach improves detection accuracy and robustness, especially in highly imbalanced environments.

### 1. XGB Classifier Conclusion

As discussed above the data is highly imbalanced and to address this issue it we used SMOTE to balance the class distribution. This model was evaluated both before and after applying SMOTE in order to analyze the impact of data balancing on model performance.

**Before**

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/138e7b85-3a90-4ca1-be00-48963cfd43be" />

**After**

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/b7e918f6-11b9-43d1-b89d-f812c38c089c" />

* When training on the original imbalanced dataset, the model achieved a loss of 0.053% (accuracy of 99.947%). After balancing the dataset using SMOTE, the loss was reduced to 0.017% (accuracy of 99.983%), indicating an slight improvement in predictive performance.
* The consistently high test accuracy demonstrates that XGBoost is highly effective at learning discriminative patterns in large-scale transaction data. 
* Overall, the results show that XGBoost performs robustly on large, imbalanced datasets and benefits from class balancing techniques such as SMOTE.
  
### 2. Autoencoder NN Conclusion

* The autoencoder demonstrates strong reconstruction capability, achieving a low and stable mean squared error of 0.0336 on the test set. The convergence of training and test loss indicates effective learning of normal transaction patterns without overfitting.
* Given the highly imbalanced nature of the dataset, the unsupervised learning approach provides a robust mechanism for detecting anomalous transactions that may represent previously unseen fraud patterns.
* This makes the Autoencoder well-suited as an anomaly detection component in a real-world fraud detection system.

<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/8796133b-0283-4160-a5bc-060dfcaa059a" />

## References

- Line 50-57: https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud?resource=download
  
