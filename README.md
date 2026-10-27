# EHC-Net_PPI_Prediction
EHC-Net: ESM2-Enhanced Hypergraph Learning for Protein–Protein Interaction Prediction

Overview

EHC-Net is a deep learning framework for protein–protein interaction (PPI) prediction that combines pretrained protein sequence representations with hypergraph learning and cross-attention.

The framework uses ESM2 to extract protein sequence embeddings, a hypergraph neural network (HGNN) to learn interaction-related representations, and a cross-attention module to model relationships between protein pairs. A multilayer perceptron (MLP) classifier predicts whether a given protein pair interacts.

This repository contains the proposed EHC-Net implementation and dataset-specific experiments for five PPI datasets, evaluated using two data-splitting protocols.

Datasets

The experiments cover the following datasets:

1. SHS27K

2. SHS148K

3. Drosophila melanogaster

4. Caenorhabditis elegans (C. elegans)

5. Escherichia coli (E. coli)

Evaluation Protocols

Two evaluation protocols are maintained separately.

1. Protein-Pair-Level Split

In this protocol, interaction pairs are divided into training, validation, and test sets. Proteins may occur in more than one partition because the splitting unit is the interaction pair.

This protocol evaluates prediction on held-out protein pairs and does not, by itself, establish generalization to previously unseen proteins.

2. Protein-Disjoint Split

In this protocol, proteins are assigned to separate training, validation, and test groups. Only interaction pairs whose two proteins belong to the same assigned group are retained for that partition. Pairs connecting proteins from different groups are excluded.

This protocol evaluates generalization to proteins that are absent from the other partitions.

Evaluation Metrics

Depending on the experiment, evaluation may include:

- Accuracy

- ROC-AUC

- Area under the precision–recall curve (AUPR)

- Precision

- Recall

- F1-score

- Matthews correlation coefficient (MCC)

