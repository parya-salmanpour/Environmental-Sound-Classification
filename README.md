# Environmental Sound Classification

This project compares audio feature representations for environmental sound classification using the ESC-10 subset of ESC-50. An RBF Support Vector Machine (SVM) is evaluated with the five predefined dataset folds.

The experiments compare Log-Mel features, MFCCs, their combination, and a combined representation that also includes temporal features.

## Dataset

The experiments use the ESC-10 subset of ESC-50:

- 400 audio recordings
- 10 sound classes
- 40 recordings per class
- 5 predefined folds

The dataset is not included in this repository. Download ESC-50 and extract it so the directory contains:

```text
ESC-50-master/
├── audio/
└── meta/
    └── esc50.csv
```

The notebook filters the 400 ESC-10 recordings using the `esc10` column in `esc50.csv`.

## Features

Three feature groups are extracted.

**Log-Mel features**

A 64-band Mel spectrogram is converted to decibel scale. The mean and standard deviation of each band are concatenated into a 128-dimensional vector.

**MFCC features**

Thirteen MFCC coefficients are calculated from the Log-Mel representation. Their means and standard deviations form a 26-dimensional vector.

**Temporal features**

Seven descriptors summarize onset strength, onset rate, onset interval regularity, onset periodicity, dominant onset period, and relative RMS energy variation.

## Classification and evaluation

All experiments use an RBF SVM with `C=1.0` and `gamma="scale"`.

The five predefined folds are used for evaluation. Each fold serves once as the test set, while the remaining four folds are used for training. Feature standardization is fitted on the training data within each split.

The four evaluated configurations are:

1. Log-Mel
2. MFCC
3. Log-Mel + MFCC
4. Log-Mel + MFCC + temporal features

Accuracy and macro F1 are reported as the mean across the five test folds. Confusion matrices and class-level classification reports are also generated from the out-of-fold predictions.

## Results

The following values are the mean scores across the five folds from the current notebook run.

| Feature representation | Accuracy | Macro F1 |
|---|---:|---:|
| Log-Mel | 72.0% | 70.7% |
| MFCC | 72.5% | 71.6% |
| Log-Mel + MFCC | 76.8% | 75.8% |
| Log-Mel + MFCC + temporal | 84.5% | 84.2% |

The combined spectral representation performs better than either spectral feature group on its own. Adding temporal descriptors gives a further improvement in this experiment.

For the final feature set, the pooled out-of-fold classification report gives a recall of 95.0% for the `clock_tick` class. This is a class-level result from the current run, not a guarantee of performance on other datasets.

## Running the notebook

Install the required packages:

```bash
pip install -r requirements.txt
```

Set `DATASET_DIR` near the beginning of the notebook to the extracted `ESC-50-master` directory, then run the notebook from top to bottom.

For Google Colab, mount Google Drive if the dataset is stored there and update `DATASET_DIR` to the corresponding folder. The notebook does not depend on a particular Drive account or folder name.

## Generated files

The notebook creates an `outputs` directory containing:

- `esc10_features.npz`
- `esc10_cv_predictions.csv`
- `esc10_cv_fold_scores.csv`
- `esc10_confusion_matrices.png`

## Scope

This version focuses on handcrafted audio features and a fixed SVM configuration. It does not include hyperparameter search, deep learning models, or evaluation on an independent external dataset.
