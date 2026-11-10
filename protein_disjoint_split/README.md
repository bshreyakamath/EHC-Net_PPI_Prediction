
# Protein-Disjoint Evaluation

This directory contains the EHC-Net implementation and experiment notebooks evaluated using protein-disjoint data splitting.

## Evaluation Protocol

In this protocol, proteins are divided into separate training, validation, and test groups before constructing the corresponding interaction-pair datasets.

Only interaction pairs for which both proteins belong to the same assigned group are retained in that group's partition. Pairs connecting proteins assigned to different groups are excluded.

This ensures that no protein is shared between the training, validation, and test partitions.

The protocol evaluates the ability of EHC-Net to predict protein–protein interactions involving proteins that were not observed in the other partitions.

## Datasets

The following three PPI datasets are evaluated using protein-disjoint splitting:

1. Drosophila melanogaster
2. Caenorhabditis elegans (C. elegans)
3. Escherichia coli (E. coli)

## Experiment Notebooks

| Dataset | Notebook |
|---|---|
| Drosophila melanogaster | `EHC-Net_Protein_disjoint_Drosophila.ipynb` |
| C. elegans | `EHC-Net_protein_disjoint_Elegans.ipynb` |
| E. coli | `EHC_Net_Protein_disjoint_ecoli.ipynb` |

## EHC-Net Architecture

EHC-Net combines the following components:

- **ESM2:** Extracts pretrained protein sequence representations.
- **Hypergraph Neural Network (HGNN):** Learns protein representations using hypergraph relationships.
- **Cross-attention:** Models relationships between the two protein representations.
- **MLP classifier:** Predicts whether a protein pair interacts.

## Reproducibility

Each notebook contains the dataset-specific preprocessing, protein assignment, interaction-pair filtering, feature extraction, hypergraph construction, model training, and evaluation procedures.

The notebooks should be executed in the order of their cells in a compatible Python environment with the required dependencies installed.

The number of proteins assigned to each partition and the number of retained interaction pairs may differ across datasets. Refer to the corresponding notebook for the exact implementation and split statistics.

## Evaluation Metrics

Depending on the dataset and experiment, the reported metrics include:

- Accuracy
- ROC-AUC
- AUPR
- Precision
- Recall
- F1-score
- Matthews correlation coefficient (MCC)
