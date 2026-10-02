# Predictive Detection of Network Intrusions Using Machine Learning

## Project Overview
This project implements a Machine Learning-based Network Intrusion Detection System (NIDS) using the NSL-KDD dataset. It classifies network connection records into five categories:

- **Normal** — legitimate network traffic
- **DoS** — Denial of Service attacks
- **Probe** — scanning and reconnaissance activity
- **R2L** — Remote-to-Local attacks
- **U2R** — User-to-Root attacks

The notebook follows a complete predictive modelling workflow: data understanding, preprocessing, model training, evaluation, and interpretation.

## Dataset
**Source:** [NSL-KDD Dataset on Kaggle](https://www.kaggle.com/datasets/hassan06/nslkdd)

The notebook expects the standard NSL-KDD training and testing text files:
- `KDDTrain+.txt`
- `KDDTest+.txt`

Download the dataset from Kaggle and place these files in the same directory as the notebook. If the downloaded filenames or locations differ, update `TRAIN_PATH` and `TEST_PATH` in the notebook.

The standard files contain 41 connection features, an attack label, and a difficulty field. The difficulty field and original attack label are excluded from model inputs to avoid target leakage.

## Project Files
```text
project/
├── NSL_KDD_Intrusion_Detection.ipynb
├── README.md
├── KDDTrain+.txt       # Download separately from Kaggle
└── KDDTest+.txt        # Download separately from Kaggle
```

The dataset files are not included in this project; obtain them from the Kaggle source above.

## Requirements
- Python 3.9 or later
- Jupyter Notebook or Google Colab
- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn
- xgboost (optional, for the XGBoost model)

Install the dependencies with:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost jupyter
```

If you do not want to use XGBoost, you can omit it; the notebook will still run Random Forest and SVM.

## How to Run
1. Clone or download this project.
2. Download the NSL-KDD dataset from the Kaggle link.
3. Extract the dataset and place `KDDTrain+.txt` and `KDDTest+.txt` beside the notebook.
4. Install the required Python packages.
5. Start Jupyter Notebook:
   ```bash
   jupyter notebook
   ```
6. Open `NSL_KDD_Intrusion_Detection.ipynb`.
7. Run the cells from top to bottom.

For Google Colab, upload the notebook and dataset files, or mount Google Drive and update the dataset paths accordingly.

## Methodology
1. **Data understanding:** inspect dimensions, data types, missing values, duplicates, label counts, and class distribution.
2. **Target grouping:** map individual attack names to Normal, DoS, Probe, R2L, or U2R.
3. **Preprocessing:** one-hot encode categorical features (`protocol_type`, `service`, and `flag`) and standardize numerical features.
4. **Class imbalance:** inspect class counts and use class-balanced weighting during model fitting.
5. **Modelling:** train and compare Random Forest, Support Vector Machine (SVM), and XGBoost (if installed).
6. **Evaluation:** calculate accuracy, macro precision, macro recall, macro F1-score, classification reports, and confusion matrices.
7. **Interpretation:** visualize Random Forest feature importance.

## Evaluation Metrics
- **Accuracy:** proportion of all predictions that are correct.
- **Precision:** proportion of predicted instances of a class that are correct.
- **Recall:** proportion of actual instances of a class that are identified correctly.
- **F1-score:** harmonic mean of precision and recall.
- **Confusion matrix:** shows correct and incorrect predictions for each class.

Macro-averaged precision, recall, and F1-score give each class equal weight, which is useful when classes are imbalanced.

## Important Notes
- Run all notebook cells to generate the actual metrics and plots. Do not report placeholder or assumed performance values.
- Review the printed unmapped attack labels during preprocessing. The notebook excludes labels grouped as `Other`; if any appear, verify their correct category before interpreting results.
- The provided train and test files are kept separate. Preprocessing is fitted using training data and then applied to test data.
- XGBoost is optional and requires the `xgboost` package.
- Results on NSL-KDD are benchmark results and do not guarantee performance on modern live network traffic. Real deployment would require testing on representative current traffic and monitoring false positives and data drift.

## Expected Outputs
After running the notebook, it produces:
- Dataset inspection and class distribution output
- Class distribution visualization
- Model comparison table
- Classification reports for each trained model
- Confusion matrices
- Model performance comparison chart
- Random Forest feature importance chart
- Example prediction for a test record


