# Immunogenicity MHC 2

Runs immunogenicity MHC class II evaluation on a batch of protein structures, predicting peptide binding and immunogenicity scores for specified MHC II alleles.

<img src="/images/nodes/biotech/functional-prediction/immunogenicity-mhc-2.png" alt="Immunogenicity MHC 2" class="rounded-lg">

## Quick Start

1. Connect a PDB batch to the node's input.
2. Specify the MHC class II allele and window size as needed.
3. Run the node to obtain immunogenicity scores as a CSV table.

## Setup Guide

### 1. Prepare Input Data
1. Generate or load protein structures in PDB format.
2. Ensure all structures are properly formatted and batch IDs are unique.

### 2. Configure Node Parameters
1. Set the `allele_name` to the desired MHC II allele (e.g., `DRB1*01:01`).
2. Adjust `window_size` (default: 15) to control the sliding window for peptide prediction.

### 3. Run and Retrieve Results
1. Execute the node in your workflow.
2. Download or inspect the resulting immunogenicity scores CSV.

## Basic Usage

### Immunogenicity Scoring
* Predicts MHC II immunogenicity for each input protein structure.
* Supports batch processing of multiple PDBs.
* Outputs a merged CSV table with scores for all provided structures.

## Configuration

### Required Inputs
| Field        | Description                                                        | Type   | Example      |
|--------------|--------------------------------------------------------------------|--------|--------------|
| pdb          | Structures to evaluate.                                            | PDB    | See PDB docs |
| allele_name  | MHC class II allele for peptide binding prediction.                | STRING | DRB1*01:01   |
| window_size  | Sliding window size for peptide prediction (11–30, default: 15).   | INT    | 15           |

### Optional Inputs
*None*

### Outputs
| Field     | Description                              | Example        |
|-----------|------------------------------------------|----------------|
| score.csv | Immunogenicity scores of the proteins.   | See below      |

## Best Practices

### Input Preparation
* Use unique IDs for each PDB in a batch to avoid result merging issues.
* Validate PDB formatting before running the node.

### Parameter Selection
* Choose `allele_name` relevant to your biological context.
* Adjust `window_size` based on peptide length of interest (typically 15 for MHC II).

## Troubleshooting

### Common Issues
* **Empty or malformed PDB input**: Ensure all input structures are valid and non-empty.
* **Incorrect allele name**: Use standard allele nomenclature (e.g., `DRB1*01:01`).
* **Window size out of range**: Set `window_size` between 11 and 30.

### Need Help?
* Contact support for further assistance.
