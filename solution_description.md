# Cardiomegaly Detection - Solution Overview

## Problem Statement
Prepare a solution for the **classification problem** of detecting **cardiomegaly** from data about chest X-ray images.

## Solution Development

### Data Preprocessing
- **Type conversion**: converted string-encoded numerical columns to numeric types
- **Feature engineering**: Created ratio-based features based on the high correlation between heart size and disease:
  - Width Ratio = Heart width / Lung width
  - Area Ratio = Heart area / Lung area
- **Stratified splitting**: preserving class proportions

### Model Training
Tested 3 algorithms due to the nonlinear nature of the problem:

**1. Support Vector Classifier (SVC)**: avoids overfitting, effective in high-dimensional spaces, versatile with different kernels (requires feature scaling)

**2. Random Forest Classifier**: works well with a lot of dimensions, handles nonlinear relationships, robust to overfitting, high stability

**3. K-Nearest Neighbors (KNN) - SELECTED**: easily captures local patterns; simple and effective (requires feature scaling)

### Hyperparameter Optimization

#### Framework: **Optuna**

**Advantages of Optuna**:

- **Bayesian Optimization**: learns from previous trials; much more efficient than grid search or random search
- **Pruning of unpromising trials**: terminates unpromising trials early, reducing computational time
- **Flexible Search Spaces**: Supports continuous, discrete, and categorical parameters


**Configuration:**
- **Validation**: 5-fold stratified cross-validation: preserves class distribution in each fold, stable, prevents overfitting
- **Metric**: F1_macro: balances precision and recall, treats both classes equally (important for imbalanced data), 
- **Preprocessing**: Pipeline with StandardScaler for SVC and KNN

### Model Selection

After hyperparameter optimization, **K-Nearest Neighbors (KNN)** was selected as the final model based on the highest cross-validation F1_macro score.
### Evaluating

Given the small dataset, multiple evaluation approaches were used:

#### 1. Hold-out Test Set Evaluation
- Standard 80/20 split for initial assessment
- Low representativeness due to small test set; results are highly sensitive to the specific train/test split

#### 2. Cross-Validated Evaluation
- 5-fold stratified cross-validation on full dataset
- More stable estimates, uses all available data

#### 3. Threshold-Independent Metrics
- **ROC Curve and AUC**: Measure the model’s ability to distinguish between classes across all possible decision thresholds
- **Precision-Recall Curve and AP**: Capture the trade-off between precision and recall over varying thresholds; especially useful for highly imbalanced datasets, where the ROC curve can be too optimistic.

### Performance Metrics

Both **binary** (positive class only) and **macro** (both classes equally weighted) metrics were computed:

- **Binary metrics** focus on the positive class (cardiomegaly detection) - clinically most critical
- **Macro metrics** prevent the model from ignoring the minority class (healthy patients)


**Metrics Used:**
- **Accuracy**: The proportion of correct predictions relative to all samples
- **F1 Score**: Harmonic mean of precision and recall
- **Precision**: Proportion of positive predictions that are correct
- **Recall**: Proportion of actual positives correctly identified
- **Confusion Matrix**: Visualizes the results of classification, distinguishing between correct and incorrect model predictions
- **ROC Curve**: Plots True Positive Rate vs False Positive Rate across all decision thresholds
- **AUC (Area Under Curve)**: Measures ability for class separation
- **PR Curve**: Plots Precision vs Recall across all decision thresholds
- **AP (Average Precision)**: Reflects the model's ability to balance precision and recall


## Evaluation Results

### Cross-Validation Performance

| Metric | Binary| Macro|
|--------|-------|------|
| **Accuracy** | 83.93% | 83.93% |
| **F1 Score** | 89.98% | 73.32% |
| **Precision** | 87.14% | 78.57% |
| **Recall** | 93.33% | 71.67% |

**AUC**: 0.855  
**AP**: 0.933

### Test Set Performance

| Metric | Binary| Macro|
|--------|-------|------|
| **Accuracy** | 75.00% | 75.00% |
| **F1 Score** | 83.33% | 66.67% |
| **Precision** | 83.33% | 66.67% |
| **Recall** | 83.33% | 66.67% |

## Conclusions
**Strong points**:
- **High recall**: Minimizes missed diagnoses
- **Good precision**: Limits false alarms
- **Strong PR**: Excellent precision-recall trade-off
- **Good AUC**: Solid discrimination ability

**Special features**:
- **Better at detecting sick patients**: Due to more positive cases in the dataset, the model achieves better performance in identifying disease cases
- **Sensitivity**: Prefers false positives over false negatives

**Limitations**:
- **Small dataset**: High uncertainty in estimates
- **Low test set representativeness**: hold-out estimates are highly sensitive to the specific train/test split 
- **Requires validation**: The model must be validated on a larger, independent dataset to confirm its real-world performance