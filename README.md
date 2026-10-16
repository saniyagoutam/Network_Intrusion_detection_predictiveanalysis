# Predictive Detection of Network Intrusions Using Machine Learning

A comparative study on the **NSL-KDD** and **UNSW-NB15** datasets using Random Forest, Support Vector Machine (SVM) and XGBoost.

## Project Overview

This project implements a Machine Learning-based Network Intrusion Detection System (NIDS) and applies the same predictive-modelling workflow to two benchmark datasets.

| | NSL-KDD | UNSW-NB15 |
|---|---|---|
| Prediction task | 5 classes | 10 classes |
| Classes | Normal, DoS, Probe, R2L, U2R | Normal, Generic, Exploits, Fuzzers, DoS, Reconnaissance, Analysis, Backdoor, Shellcode, Worms |
| Training / test records | 125,973 / 22,544 | 175,341 / 82,332 |
| Notebook | `NSL_KDD_Intrusion_Detection.ipynb` | `UNSW_NB15_Intrusion_Detection.ipynb` |

Each notebook follows the same workflow: data understanding, preprocessing, model training, evaluation and interpretation.

## Datasets

### NSL-KDD
**Source:** [NSL-KDD Dataset on Kaggle](https://www.kaggle.com/datasets/hassan06/nslkdd)

Files used: `KDDTrain+.txt` and `KDDTest+.txt`. Each record has 41 connection features, an attack label and a difficulty field. The difficulty field and the original attack label are excluded from the model inputs to avoid target leakage.

### UNSW-NB15
**Source:** [UNSW-NB15 dataset (UNSW Canberra)](https://research.unsw.edu.au/projects/unsw-nb15-dataset)

Files used: `UNSW_NB15_training-set.csv` and `UNSW_NB15_testing-set.csv`. On the official download page they are inside the folder *CSV Files → Training and Testing Sets*. Copies are also available on Kaggle; check that the file names and the record counts match (175,341 and 82,332).

Each record has 42 flow features, an attack category (`attack_cat`), a 0/1 flag (`label`) and a row number (`id`). The row number, the 0/1 flag and the category text are excluded from the model inputs.

> Place the data files in the same folder as the notebooks. If they are not included in your copy of this repository, download them from the sources above. If your files have different names or locations, update `TRAIN_PATH` and `TEST_PATH` in the notebook.

## Project Files

```text
project/
├── NSL_KDD_Intrusion_Detection.ipynb
├── UNSW_NB15_Intrusion_Detection.ipynb
├── README.md
├── KDDTrain+.txt                    # NSL-KDD training file
├── KDDTest+.txt                     # NSL-KDD testing file
├── UNSW_NB15_training-set.csv       # UNSW-NB15 training file
└── UNSW_NB15_testing-set.csv        # UNSW-NB15 testing file
```

## Requirements

- Python 3.9 or later
- Jupyter Notebook or Google Colab
- pandas, numpy, matplotlib, seaborn, scikit-learn
- xgboost (optional, for the XGBoost model)

Install the dependencies with:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost jupyter
```

If you do not want to use XGBoost, you can omit it; the notebooks will still run Random Forest and SVM.

## How to Run

1. Clone or download this project.
2. Get the dataset files (see **Datasets**) and place them beside the notebooks.
3. Install the required Python packages.
4. Start Jupyter Notebook:
   ```bash
   jupyter notebook
   ```
5. Open `NSL_KDD_Intrusion_Detection.ipynb` or `UNSW_NB15_Intrusion_Detection.ipynb`.
6. Run the cells from top to bottom.

For Google Colab, upload the notebook and the data files, or mount Google Drive and update the dataset paths.

**Runtime:** the NSL-KDD notebook takes a few minutes. The UNSW-NB15 notebook takes about 25 minutes on a single CPU core (Random Forest is the slowest step, then the SVM); it is faster on a multi-core machine.

## Methodology

1. **Data understanding:** inspect dimensions, data types, missing values, duplicates, label counts and class distribution.
2. **Target definition:**
   - *NSL-KDD:* map the individual attack names to Normal, DoS, Probe, R2L or U2R.
   - *UNSW-NB15:* use the supplied attack categories (`attack_cat`) as the ten classes; no manual grouping is needed.
3. **Preprocessing:** one-hot encode the categorical features and standardise the numerical features. The transformer is fitted on the training data only and then applied to the test data.
   - *NSL-KDD:* `protocol_type`, `service`, `flag` (84 columns) plus 38 numeric features = 122 inputs.
   - *UNSW-NB15:* `proto`, `service`, `state` (155 columns) plus 39 numeric features = 194 inputs.
4. **Class imbalance:** inspect class counts and use class-balanced weighting during model fitting.
5. **Modelling:** train and compare Random Forest, SVM (RBF kernel) and XGBoost (if installed). The settings are identical for both datasets.
6. **Evaluation:** accuracy, macro precision, macro recall, macro F1-score, classification reports and confusion matrices. The UNSW-NB15 notebook also reports the attack miss rate and the false-alarm rate.
7. **Interpretation:** Random Forest feature importance and an example prediction on one test record.

## Evaluation Metrics

- **Accuracy:** proportion of all predictions that are correct.
- **Precision:** proportion of predicted instances of a class that are correct.
- **Recall:** proportion of actual instances of a class that are identified correctly.
- **F1-score:** harmonic mean of precision and recall.
- **Macro average:** the unweighted mean over the classes, so every class counts equally. This is useful when classes are imbalanced.
- **Confusion matrix:** shows correct and incorrect predictions for each class.

## Results

Results from running the notebooks on the official test files.

### NSL-KDD (22,075 evaluated test records)

| Model | Accuracy | Macro precision | Macro recall | Macro F1 |
|---|---|---|---|---|
| XGBoost | 0.7903 | 0.8069 | 0.5669 | 0.5973 |
| SVM | 0.7646 | 0.6961 | 0.5521 | 0.5403 |
| Random Forest | 0.7394 | 0.7552 | 0.4620 | 0.4682 |

### UNSW-NB15 (82,332 test records)

| Model | Accuracy | Macro precision | Macro recall | Macro F1 |
|---|---|---|---|---|
| XGBoost | 0.6757 | 0.5164 | 0.6565 | 0.5006 |
| Random Forest | 0.7493 | 0.5316 | 0.5134 | 0.4832 |
| SVM (60,000-record sample) | 0.6354 | 0.4149 | 0.5546 | 0.3811 |

### Main observations

- XGBoost has the best macro F1-score on both datasets. The best accuracy differs: XGBoost on NSL-KDD, Random Forest on UNSW-NB15.
- **NSL-KDD:** Normal, DoS and Probe are detected well, but R2L and U2R are mostly missed (32–43% of all attacks are predicted as Normal, while false alarms stay at 2.6–3.2%). The test file also contains attack types that never appear in training, and only a small share of them is flagged.
- **UNSW-NB15:** Generic and Reconnaissance are detected well; Analysis, Backdoor and DoS are detected poorly. Almost no attack is predicted as Normal (0.2–2.8%), but 23–42% of normal flows are flagged as attacks.
- Important features on both datasets are traffic volume (bytes and packet size) and statistics about recent connections to the same host or service.

## Important Notes

- Run all notebook cells to generate the actual metrics and plots. Do not report placeholder or assumed performance values. Small numerical differences may occur with different library versions.
- **UNSW-NB15 SVM:** the SVM becomes very slow on 175,341 records, so it is trained on a stratified random sample of 60,000 records (`SVM_TRAIN_LIMIT` in the notebook). Set it to `None` to train on the full training set; this can take an hour or more. Random Forest and XGBoost always use the full training set.
- **UNSW-NB15 duplicates:** about 38.6% of the training rows (67,601) are duplicates once the `id` column is ignored. They are kept, because the official partition is used as published.
- **NSL-KDD label mapping:** the grouping step excludes records labelled `Other`. In the current notebook the labels `saint`, `named` and `httptunnel` in the test file are not matched by the lookup sets, so 469 test records (2.08%) are excluded and the metrics refer to the remaining 22,075 records. Review the printed unmapped labels before interpreting results.
- The provided train and test files are kept separate. Preprocessing is fitted on the training data and then applied to the test data.
- XGBoost is optional and requires the `xgboost` package.
- Results on these datasets are benchmark results and do not guarantee performance on modern live network traffic. Real deployment would require testing on representative current traffic and monitoring of false positives and data drift.

## Expected Outputs

After running each notebook, it produces:
- Dataset inspection and class distribution output
- Class distribution visualisation
- Model comparison table and performance comparison chart
- Classification reports for each trained model
- Confusion matrices
- Random Forest feature importance chart
- Example prediction for a test record
- (UNSW-NB15 notebook) attack miss rate and false-alarm table, and an optional `unsw_results.json` results file

## References

1. M. Tavallaee, E. Bagheri, W. Lu and A. A. Ghorbani, "A detailed analysis of the KDD CUP 99 data set," *IEEE CISDA*, 2009. (NSL-KDD)
2. N. Moustafa and J. Slay, "UNSW-NB15: A comprehensive data set for network intrusion detection systems," *MilCIS*, 2015. (UNSW-NB15)

If you use the UNSW-NB15 dataset, please cite the dataset papers as requested on the official dataset page.

## Author

**Saniya Goutam**, B.Tech Computer Science, Symbiosis Institute of Technology, Nagpur
Course: Predictive Analytics (CA3 Component 2) · Guide: Dr. Deepak Asudani
