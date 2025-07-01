# Immunogenicity Class 1

Runs immunogenicity class 1 evaluation on the provided batch of proteins, predicting MHC class I binding and immunogenicity scores.

<img src="/images/nodes/biotech/functional-prediction/immunogenicity-class-1.png" alt="Immunogenicity Class 1" class="rounded-lg">

## Quick Start

1. Connect a PDB batch to the node's input.
2. Specify the MHC class I allele and window size as needed.
3. Run the node to obtain immunogenicity scores as a CSV.

## Setup Guide

### 1. Prepare Input Data
1. Generate or load protein structures in PDB format.
2. Ensure each structure has a unique identifier.

### 2. Configure Node Parameters
1. Set the `c_allele` to the desired MHC class I allele (e.g., HLA-A0101).
2. Adjust `c_window_size` for the sliding window (default: 9).

### 3. Run Evaluation
1. Connect the node in your workflow and execute to receive results.

## Basic Usage

### Immunogenicity Scoring
* Predicts immunogenicity for each input protein structure.
* Supports batch processing of multiple PDBs.
* Outputs a merged CSV table of scores.

## Configuration

### Required Inputs
| Field        | Description                                                                 | Type   | Example     |
|--------------|-----------------------------------------------------------------------------|--------|-------------|
| pdb          | Structures to evaluate.                                                     | PDB    | See PDB doc |
| c_allele     | MHC class I allele forming the pMHCI complex for prediction.                | STRING | HLA-A0101   |
| c_window_size| Sliding window size for peptide binding prediction.                         | INT    | 9           |

### Optional Inputs
*None*

### Outputs
| Field     | Description                        | Example         |
|-----------|------------------------------------|-----------------|
| score.csv | Immunogenicity scores of proteins. | See CSV output  |

## Best Practices

### Input Preparation
* Use well-formed PDB files with unique IDs for each structure.
* Choose the appropriate MHC allele relevant to your study.

### Parameter Selection
* Default window size (9) is suitable for most MHC class I predictions.
* Adjust window size only if you have specific requirements.

## Troubleshooting

### Common Issues
* **Empty or invalid PDB input**: Ensure all input structures are valid and uniquely identified.
* **Incorrect allele format**: Use standard allele names (e.g., HLA-A0101).

### Need Help?
* Contact support for further assistance.
