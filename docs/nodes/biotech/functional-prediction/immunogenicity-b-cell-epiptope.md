# Immunogenicity B Cell Epiptope

Runs immunogenicity B-cell epitope evaluation on a batch of protein structures, predicting potential immunogenic regions using a sliding window approach.

<img src="/images/nodes/biotech/functional-prediction/immunogenicity-b-cell-epiptope.png" alt="Immunogenicity B Cell Epiptope" class="rounded-lg">

## Quick Start

1. Prepare your protein structure(s) in PDB format.
2. Set the desired window size for epitope prediction.
3. Run the node to obtain immunogenicity scores as a CSV table.

## Setup Guide

### 1. Prepare Input Data
1. Ensure your protein structures are available in PDB format.
2. Load the PDB(s) using the appropriate node (e.g., Load PDB).

### 2. Configure Node Parameters
1. Specify the window size for the sliding window (default: 10).
2. (Optional) Adjust the mode for testing or mock runs if needed.

### 3. Run the Node
1. Connect the required inputs and execute the node.
2. Review the output CSV for immunogenicity scores.

## Basic Usage

### Predicting B-cell Epitope Immunogenicity
* Input one or more PDB structures.
* Set the window size to define the peptide length for prediction.
* Obtain a CSV table with immunogenicity scores for each input structure.

## Configuration

### Required Inputs
| Field        | Description                                                        | Type   | Example |
|--------------|--------------------------------------------------------------------|--------|---------|
| pdb          | Structures to evaluate.                                            | PDB    | {"sample1": "...PDB content..."} |
| window_size  | Sliding window size to construct peptides for prediction.           | INT    | 10      |

### Optional Inputs
*None*

### Outputs
| Field     | Description                          | Example |
|-----------|--------------------------------------|---------|
| score.csv | Immunogenicity scores of the proteins.| CSV file with scores |

## Best Practices

### Input Preparation
* Ensure PDB files are properly formatted and contain valid protein structures.
* Use unique identifiers for each structure to avoid confusion in output tables.

### Parameter Selection
* Choose a window size appropriate for the epitope length relevant to your study.
* For typical B-cell epitope prediction, a window size of 10 is standard, but adjust as needed.

## Troubleshooting

### Common Issues
* **Empty or malformed PDB input**: Ensure all input structures are valid and correctly formatted.
* **No scores in output**: Check that the window size is appropriate and input structures are not empty.

### Need Help?
* Contact support for further assistance.
