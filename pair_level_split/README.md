# Protein-Pair-Level Evaluation

This directory contains the EHC-Net implementation and experiment notebooks evaluated using protein-pair-level data splitting.

## Evaluation Protocol

In this protocol, protein interaction pairs are divided into training, validation, and test sets. The splitting unit is the protein pair rather than the individual protein.

Consequently, the same protein may occur in multiple partitions, although an identical unordered protein pair is not shared between the training, validation, and test sets.

This protocol evaluates prediction performance on held-out interaction pairs. It does not guarantee generalization to previously unseen proteins.

## Datasets

The following five PPI datasets are evaluated:

1. SHS27K
2. SHS148K
3. Drosophila melanogaster
4. Caenorhabditis elegans (C. elegans)
5. Escherichia coli (E. coli)

## Experiment Notebooks

| Dataset | Notebook |
|---|---|
| SHS27K | `EHC-Net_SHS27K.ipynb` |
| SHS148K | `EHC-Net_SHS148K.ipynb` |
| Drosophila melanogaster | `EHC-Net_Drosophila.ipynb` |
| C. elegans | `EHC-Net_Elegans.ipynb` |
| E. coli | `EHC-Net_Ecoli.ipynb` |

## EHC-Net Architecture

EHC-Net combines the following components:

- **ESM2:** Extracts pretrained protein sequence representations.
- **Hypergraph Neural Network (HGNN):** Learns protein representations using hypergraph relationships.
- **Cross-attention:** Models relationships between the two protein representations.
- **MLP classifier:** Predicts whether a protein pair interacts.

## Reproducibility

Each notebook contains the dataset-specific preprocessing, data splitting, feature extraction, hypergraph construction, model training, and evaluation procedures.

The notebooks should be executed in the order of their cells in a compatible Python environment with the required dependencies installed.

Dataset-specific preprocessing and negative-sampling procedures may differ. Refer to the corresponding notebook for the exact implementation used for each dataset.

## Evaluation Metrics

Depending on the dataset and experiment, the reported metrics include:

- Accuracy
- ROC-AUC
- AUPR
- Precision
- Recall
- F1-score
- Matthews correlation coefficient (MCC)
