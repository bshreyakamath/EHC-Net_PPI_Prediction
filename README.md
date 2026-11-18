# EHC-Net_PPI_Prediction
EHC-Net: ESM2-Enhanced Hypergraph Learning for Protein–Protein Interaction Prediction

## Overview

EHC-Net is a deep learning framework for protein–protein interaction (PPI) prediction that combines pretrained protein sequence representations with hypergraph learning and cross-attention.

The framework uses ESM2 to extract protein sequence embeddings, a hypergraph neural network (HGNN) to learn interaction-related representations, and a cross-attention module to model relationships between protein pairs. A multilayer perceptron (MLP) classifier predicts whether a given protein pair interacts.

This repository contains the proposed EHC-Net implementation and dataset-specific experiments for five PPI datasets, evaluated using two data-splitting protocols.

## Datasets

The experiments cover the following datasets:

1. SHS27K

2. SHS148K

3. Drosophila melanogaster

4. Caenorhabditis elegans (C. elegans)

5. Escherichia coli (E. coli)

## Evaluation Protocols

Two evaluation protocols are maintained separately.

1. Protein-Pair-Level Split

In this protocol, interaction pairs are divided into training, validation, and test sets. Proteins may occur in more than one partition because the splitting unit is the interaction pair.

This protocol evaluates prediction on held-out protein pairs and does not, by itself, establish generalization to previously unseen proteins.

2. Protein-Disjoint Split

In this protocol, proteins are assigned to separate training, validation, and test groups. Only interaction pairs whose two proteins belong to the same assigned group are retained for that partition. Pairs connecting proteins from different groups are excluded.

This protocol evaluates generalization to proteins that are absent from the other partitions.

## Hypergraph Construction

For the SHS27K protein-pair-level experiment, the hypergraph is constructed using positive interaction pairs from the training partition only.

An adjacency-based neighborhood is created for each protein using its positive training interactions. Each hyperedge contains a central protein and its associated positive training neighbors. The resulting hypergraph is represented using a hyperedge index and combined with the pretrained ESM2 protein embeddings as input to the HGNN encoder.

This construction ensures that the hypergraph interaction structure is derived from the training partition rather than from the validation or test interaction labels.


## Reproducibility and Random Seed

All random operations in the SHS27K protein-pair-level experiment use a fixed random seed of 42.

The seed is applied to Python's random module, NumPy, and PyTorch. CUDA random seeds are also initialized when a GPU is available. cuDNN deterministic mode is enabled and benchmarking is disabled.

The dataset is divided using stratified random sampling to preserve the class distribution across the partitions.

The initial split assigns 80% of the interaction pairs to the training and validation pool and 20% to the test set. Subsequently, 10% of the remaining training and validation pool is allocated to validation.

Therefore, the resulting approximate proportions are:

- Training: 72%
- Validation: 8%
- Testing: 20%

The same random seed (42) is used for both splitting operations.


## Evaluation Metrics

Depending on the experiment, evaluation may include:

- Accuracy

- ROC-AUC

- Area under the precision–recall curve (AUPR)

- Precision

- Recall

- F1-score

- Matthews correlation coefficient (MCC)


## Environment and Dependencies

The EHC-Net experiments were conducted using Google Colab with GPU acceleration.

The following package versions were recorded during the SHS27K pair-level experiment:

| Dependency | Version |
|---|---|
| PyTorch | 2.11.0+cu128 |
| PyTorch Geometric | 2.8.0 |
| NumPy | 2.0.2 |
| Fair-ESM | 2.0.0 |
| THOP | 0.1.1.post2209072238 |
| tqdm | 4.67.3 |

The following packages were installed in the Colab environment:

```bash
pip install fair-esm
pip install torch-geometric
pip install tqdm
pip install thop
```

PyTorch was available in the Google Colab environment.

**Note:** Package versions may differ across datasets and experimental runs. The versions listed above are those recorded for the SHS27K pair-level experiment.

## EHC-Net Model Configuration and Hyperparameters

The SHS27K EHC-Net experiment uses the following model configuration and training hyperparameters.

| Parameter | Value |
|---|---|
| ESM2 pretrained model | esm2_t6_8M_UR50D |
| ESM2 representation layer | 6 |
| Protein embedding dimension | 320 |
| Sequence truncation length | 1,022 |
| Protein encoder | 320 → 512 → 320 |
| HGNN layers | 320 → 512 → 256 → 128 |
| Cross-attention dimension | 256 |
| Attention heads | 8 |
| Classifier | 1024 → 512 → 128 → 1 |
| Dropout | 0.3 |
| Loss function | BCEWithLogitsLoss |
| Optimizer | Adam |
| Learning rate | 0.001 |
| Batch size | 128 |
| Maximum epochs | 20 |
| Early stopping patience | 5 |
| Model selection criterion | Validation ROC-AUC |
| Classification threshold | 0.5 |

The model is trained using binary cross-entropy with logits. The best model is selected according to validation ROC-AUC and saved for subsequent test evaluation.


## Dataset Availability and Preparation

The original datasets and processed input files are not included in this repository. Users must obtain the required dataset files before executing the experiment notebooks.

### SHS27K and SHS148K

For these datasets, users must download the corresponding interaction data (`actions` files) and protein sequence data (`sequences` files) from their original sources.

The downloaded files should be made available in the locations expected by the corresponding notebooks.

### SHS27K Data Preprocessing

The SHS27K interaction data are loaded from `SHS27k.actions.txt`, and protein sequences are obtained from `SHS27k.seqs.tsv`.

The protein identifiers from the interaction file are converted into protein pairs, and all extracted interaction pairs are assigned a positive label (1). Duplicate interactions are removed by representing each unordered protein pair in a canonical sorted order.

Negative interaction pairs are generated through random sampling of two distinct protein identifiers from the available sequence collection. Candidate pairs that already exist in the positive interaction set are rejected. Negative pairs are sampled until their number equals the number of positive pairs, producing an initially balanced dataset.

The positive and negative pairs are combined, duplicate unordered pairs are removed, and the resulting dataset is divided into training, validation, and testing partitions using stratified random sampling with seed 42.

Protein sequences are subsequently used to generate pretrained ESM2 embeddings. Sequences are truncated to a maximum length of 1,022 amino acids. The ESM2 model `esm2_t6_8M_UR50D` is used to extract representations from layer 6. Residue-level representations are mean-pooled to obtain 320-dimensional protein embeddings.

Finally, interaction pairs containing proteins without generated embeddings are excluded from the corresponding partitions.


### Drosophila melanogaster, C. elegans, and E. coli

The corresponding experiment notebooks use direct download URLs to retrieve the required dataset files from their original data sources.

The files are downloaded programmatically using `wget` and processed within the notebooks. Users do not need to manually upload these original dataset files before running the download cells.

### Important

Dataset file names, paths, preprocessing steps, and input formats are specified in the corresponding experiment notebooks. Please follow the instructions and execution order provided in each notebook.

## Reproducing the SHS27K Pair-Level Experiment

The SHS27K experiment can be reproduced using the `EHC-Net_SHS27K.ipynb` notebook in the `pair_level_split/` directory.

1. Open the notebook using Google Colab.
2. Upload or provide access to `SHS27k.actions.txt` and `SHS27k.seqs.tsv`.
3. Execute the notebook cells sequentially.
4. Install the required dependencies when prompted by the notebook.
5. Execute the preprocessing cells to generate positive and negative interaction pairs.
6. Generate ESM2 protein embeddings and prepare the training, validation, and testing datasets.
7. Construct the training hypergraph and initialize the EHC-Net model.
8. Train the model using the specified hyperparameters.
9. Load the best validation-selected model and execute the final test evaluation.

The notebook generates the following processed dataset files:

- `SHS27K_full_seed42_balanced.csv`
- `train_SHS27K_seed42.csv`
- `valid_SHS27K_seed42.csv`
- `test_SHS27K_seed42.csv`

The trained model checkpoint is saved as `STUDY3_shs27k.pth`.

The notebook reports Accuracy, ROC-AUC, AUPR, F1-score, Precision, Recall, MCC, and the confusion matrix.


## Repository Structure

The repository is organized into two directories corresponding to the two evaluation protocols.
```text
EHC-Net_PPI_Prediction/
│
├── README.md
│
├── pair_level_split/
│   ├── EHC-Net_Drosophila.ipynb
│   ├── EHC-Net_Ecoli.ipynb
│   ├── EHC-Net_Elegans.ipynb
│   ├── EHC-Net_SHS148K.ipynb
│   ├── EHC-Net_SHS27K.ipynb
│   └── README.md
│
└── protein_disjoint_split/
    ├── EHC-Net_Protein_disjoint_Drosophila.ipynb
    ├── EHC-Net_protein_disjoint_Elegans.ipynb
    ├── EHC_Net_Protein_disjoint_ecoli.ipynb
    └── README.md
```
## Experiment Notebooks

1. Protein-Pair-Level Split

The pair_level_split/ directory contains the EHC-Net experiment notebooks for all five datasets:

- SHS27K

- SHS148K

- Drosophila melanogaster

- Caenorhabditis elegans

- Escherichia coli

2. Protein-Disjoint Split

The protein_disjoint_split/ directory contains the experiment notebooks for:

- Drosophila melanogaster

- Caenorhabditis elegans

- Escherichia coli

Each directory also contains a separate README describing its corresponding evaluation protocol.
