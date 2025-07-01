# Evaluate Unconditional

Runs unconditional evaluation pipeline on the outputs of Alphafold or Colabfold models. Selects a folding that is most similar to the generation and returns a scores table.

<img src="/images/nodes/biotech/functional-prediction/evaluate-unconditional.png" alt="Evaluate Unconditional" class="rounded-lg">

## Quick Start

1. Connect generated proteins (PDB) to `generation_pdb` input.
2. Connect foldings (PDB) to `folding_pdb` input.
3. Run the node to obtain the best folding and evaluation scores.

## Setup Guide

### 1. Prepare Inputs
1. Generate proteins using a diffusion or design node.
2. Obtain foldings from Alphafold or Colabfold nodes.

### 2. Connect and Run
1. Connect the generated proteins to `generation_pdb`.
2. Connect the foldings to `folding_pdb`.
3. Execute the node to receive evaluation results.

## Basic Usage

### Unconditional Evaluation
* Compare generated proteins to predicted foldings.
* Select the best matching folding for each design.
* Output a CSV table with evaluation metrics (pst, rmsd, pae, plddt).

## Configuration

### Required Inputs
| Field           | Description                                 | Type | Example |
|-----------------|---------------------------------------------|------|---------|
| generation_pdb  | Generated proteins to evaluate.             | PDB  | {"design_0": "...PDB..."} |
| folding_pdb     | Foldings to evaluate proteins against.       | PDB  | {"design_0_ranked_0": "...PDB..."} |

### Optional Inputs
*None*

### Outputs
| Field            | Description                    | Example |
|------------------|-------------------------------|---------|
| best_folding.pdb | Best of the predicted foldings.| {"design_0": "...PDB..."} |
| score.csv        | Scores of the foldings (pst, rmsd, pae, plddt). | "design_0,0.95,1.2,10.5,85.0" |

## Best Practices

### Input Preparation
* Ensure that `generation_pdb` and `folding_pdb` have matching or corresponding IDs for accurate evaluation.
* Use outputs from supported generation and folding nodes for compatibility.

### Efficient Evaluation
* For large batches, consider running in TEST mode to limit evaluation to the first structure for faster feedback.

## Troubleshooting

### Common Issues
* **Mismatched IDs:** Ensure that the IDs in `generation_pdb` and `folding_pdb` correspond to the same designs.
* **Empty Outputs:** Check that both inputs contain valid PDB data.

### Need Help?
* Contact support for further assistance.
