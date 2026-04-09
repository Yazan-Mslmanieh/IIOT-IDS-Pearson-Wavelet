\# IIoT Intrusion Detection System — WUSTL-IIOT-2021



\## Overview



A machine learning-based Intrusion Detection System (IDS) for Industrial IoT environments,

built on the WUSTL-IIOT-2021 dataset. The pipeline combines Haar Wavelet Transform for

data reduction with Pearson Correlation feature selection, and evaluates 6 classifiers

before and after wavelet preprocessing.



\---



\## Dataset



\- \*\*Source:\*\* WUSTL-IIOT-2021

\- \*\*Original size:\*\* 1,194,464 rows × 49 columns

\- \*\*Traffic types:\*\* Normal, DoS, Reconnaissance, Command Injection, Backdoor

\- \*\*Class distribution:\*\* 92.7% Normal / 7.3% Attack (binary classification)



\---



\## Methodology



1\. \*\*Preprocessing\*\* — Drop identifier columns, label encode categoricals, remove outliers

&#x20;  (IQR with constraint to preserve Attack samples)

2\. \*\*Feature Selection\*\* — Pearson Correlation → top \*\*28 features\*\* selected

&#x20;  (from original 41 numeric features, computed on Train set only)

3\. \*\*Normalization\*\* — StandardScaler fitted on Train only

4\. \*\*Haar Wavelet Transform\*\* — 5 levels applied row-wise, reducing:

&#x20;  - Train: 604,202 → 18,881 rows

&#x20;  - Test:  179,065 → 5,595 rows

5\. \*\*Models trained:\*\* Random Forest, DNN (TensorFlow/Keras), LightGBM,

&#x20;  Decision Tree, Logistic Regression, Linear SVM

6\. \*\*Class imbalance handling:\*\* `class\_weight` / `scale\_pos\_weight`



\---



\## Results



\### Without Wavelet (Original Data)



| Model | Accuracy | Precision | Recall   | F1-Score | ROC-AUC |

|-------|----------|-----------|----------|----------|---------|

| RF    | 93.52%   | 52.93%    | 100.00%  | 69.22%   | 99.12%  |

| DNN   | 97.60%   | 75.25%    | 99.98%   | 85.87%   | 98.69%  |

| LGBM  | 94.55%   | 57.21%    | 100.00%  | 72.78%   | 98.94%  |

| DT    | 97.56%   | 74.93%    | 100.00%  | 85.67%   | 98.68%  |

| LR    | 97.61%   | 75.29%    | 99.99%   | 85.90%   | 98.61%  |

| SVM   | 97.55%   | 74.82%    | 99.98%   | 85.59%   | 98.31%  |



\### After Wavelet Transform (5 Levels)



| Model | Accuracy | Precision | Recall   | F1-Score | ROC-AUC | ΔF1         |

|-------|----------|-----------|----------|----------|---------|-------------|

| RF    | 93.98%   | 93.81%    | 100.00%  | \*\*96.81%\*\* | 81.45% | \*\*+27.59%\*\* |

| DNN   | 92.60%   | 95.47%    | 96.48%   | 95.97%   | 85.42%  | +10.11%     |

| LGBM  | 91.71%   | 91.68%    | 100.00%  | 95.66%   | 77.21%  | +22.88%     |

| DT    | 93.14%   | 94.15%    | 98.61%   | 96.33%   | 66.97%  | +10.66%     |

| LR    | 93.87%   | 94.64%    | 98.88%   | 96.72%   | 86.19%  | +10.82%     |

| SVM   | 91.87%   | 91.86%    | 99.96%   | 95.74%   | 87.35%  | +10.15%     |



> \*\*Best model after Wavelet: Random Forest\*\* — F1 = 96.81%, +27.59% improvement over baseline.



\---



\## Key Findings



\- Haar Wavelet Transform significantly improved \*\*F1-Score\*\* across all models

&#x20; (minimum +10%, maximum +27.59%)

\- \*\*Precision\*\* improved drastically after wavelet (RF: 52.93% → 93.81%)

\- Data reduced by \*\*96.9%\*\* (604K → 18K training rows) with no loss in Recall

\- Best without wavelet: \*\*Logistic Regression\*\* — F1 = 85.90%, AUC = 98.61%

\- Best with wavelet: \*\*Random Forest\*\* — F1 = 96.81%



\---



\## Selected Features (Top 28 — Pearson Correlation)
pLoss, sTtl, dTtl, SrcBytes, TotPkts, SrcPkts, DstPkts, DstLoss,
SAppBytes, TotBytes, DIntPkt, DstJitter, DstBytes, TotAppByte, DAppBytes,
SIntPkt, RunTime, Dur, Max, Min, Sum, Load, SrcLoad, Rate,
SrcRate, TcpRtt, DstLoad, DstRate

- Original features: 49 columns
- After dropping identifiers & preprocessing: 41 numeric features
- **After Pearson selection: 28 features**

---

## Usage

```python
import joblib, numpy as np

scaler       = joblib.load('models/scaler.pkl')
selected_idx = joblib.load('models/selected_idx.pkl')
rf_model     = joblib.load('models/rf_model.pkl')

# X: your input with feature_names columns
X_scaled = scaler.transform(X[:, selected_idx]).astype(np.float32)
predictions   = rf_model.predict(X_scaled)
probabilities = rf_model.predict_proba(X_scaled)[:, 1]
```

---

## Environment

- Python 3.10
- TensorFlow 2.19.0
- LightGBM 4.6.0
- Google Colab (GPU recommended for DNN training)

---

## License

This project is provided for **research and educational purposes only**.
Commercial use or redistribution without permission is prohibited.


