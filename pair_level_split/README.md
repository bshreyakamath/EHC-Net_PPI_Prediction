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

---

## SHS27K: Dataset-Specific Reproducibility

### 1. Data Preprocessing

The SHS27K interaction data were obtained from `SHS27k.actions.txt`, and protein sequences were obtained from `SHS27k.seqs.tsv`.

- Interaction pairs were extracted from the source interaction file.
- Duplicate and reversed duplicate (unordered) protein pairs were removed.
- Protein identifiers were matched with the corresponding sequences.
- A total of 1,690 protein sequences were available for the experiment.

### 2. Negative Sampling

Negative interaction pairs were randomly generated from the available protein sequence identifiers.

The sampling procedure:
- Excluded self-interacting pairs.
- Excluded pairs already present in the known positive interaction set.
- Generated the same number of negative pairs as positive pairs.
- Used random seed 42.

The resulting dataset contained:

| Class | Number of pairs |
|---|---:|
| Positive | 7,444 |
| Negative | 7,444 |
| **Total** | **14,888** |

The sampled negative pairs represent unobserved pairs treated as non-interactions for model training and evaluation; they are not necessarily experimentally verified non-interactions.

### 3. Data Splitting

The dataset was divided into training, validation, and test sets using stratified splitting.

- Random seed: 42
- Test fraction: 20% of the complete dataset.
- Validation fraction: 10% of the remaining training portion.

| Partition | Number of pairs |
|---|---:|
| Training | 10,719 |
| Validation | 1,191 |
| Test | 2,978 |
| **Total** | **14,888** |

No identical unordered protein pair was shared between the three partitions. However, proteins could occur in multiple partitions because this is a protein-pair-level evaluation.

### 4. Protein Sequence Embeddings

Protein sequence representations were extracted using the pretrained `esm2_t6_8M_UR50D` model.

- ESM2 representation layer: 6
- Maximum sequence length: 1,022 residues
- Pooling: Mean pooling over residue-token representations, excluding beginning-of-sequence and end-of-sequence tokens.
- Embedding dimension: 320
- Number of protein embeddings: 1,690

### 5. Hypergraph Construction

The hypergraph was constructed using positive training interactions only.

- Protein features: 320-dimensional ESM2 embeddings.
- Hyperedges: Constructed from each protein and its positive training interaction neighbors.
- Number of hyperedges: 1,550
- Hypergraph incidence index shape: `[2, 12270]`

### 6. Model Configuration

| Parameter | Setting |
|---|---|
| Protein feature dimension | 320 |
| HGNN protein encoder | 320 → 512 → 320 |
| Hypergraph convolution layers | 320 → 512 → 256 → 128 |
| Pair representation dimension | 448 |
| Projection dimension | 256 |
| Cross-attention heads | 8 |
| Classifier | 1024 → 512 → 128 → 1 |
| Dropout | 0.3 |
| Loss function | BCEWithLogitsLoss |
| Optimizer | Adam |
| Learning rate | 0.001 |
| Batch size | 128 |
| Maximum epochs | 20 |
| Early stopping patience | 5 |
| Early stopping metric | Validation ROC-AUC |
| Random seed | 42 |
