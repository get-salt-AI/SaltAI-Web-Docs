# Evaluate Unconditional Initial Guess

Runs unconditional evaluation on outputs from the Alphafold Initial Guess node. Merges initial guess scores with unconditional evaluation scores and returns the resulting scores table.

<img src="/images/nodes/biotech/functional-prediction/evaluate-unconditional-initial-guess.png" alt="Evaluate Unconditional Initial Guess" class="rounded-lg">

## Quick Start

1. Connect generated proteins (PDB) and foldings (PDB) from previous nodes.
2. Provide the initial guess score table (STRING) from Alphafold Initial Guess.
3. Run the node to obtain the best folding and merged scores.

## Setup Guide

### 1. Prepare Inputs
1. Generate proteins using a diffusion or design node.
2. Predict foldings using the Alphafold Initial Guess node.
3. Collect the score table output from Alphafold Initial Guess.

### 2. Connect and Run
1. Connect the generated PDB, folding PDB, and initial guess score STRING to this node.
2. Execute the node to receive evaluation results.

## Basic Usage

### Evaluate Initial Guess
* Assess the quality of generated proteins using foldings and initial guess scores.
* Use in workflows requiring evaluation of Alphafold Initial Guess outputs.

## Configuration

### Required Inputs
| Field               | Description                                                      | Type   | Example         |
|---------------------|------------------------------------------------------------------|--------|-----------------|
| generation_pdb      | Generated proteins to evaluate                                   | PDB    | {"id": "..."} |
| folding_pdb         | Foldings to evaluate proteins against                            | PDB    | {"id": "..."} |
| initial_guess_score | Score table from initial guess prediction (from Alphafold node)  | STRING | "score.sc"      |

### Optional Inputs
*None*

### Outputs
| Field            | Description                        | Example         |
|------------------|------------------------------------|-----------------|
| best_folding.pdb | Best of the predicted foldings      | {"id": "..."} |
| score.csv        | Scores of the foldings (csv table)  | "score.csv"     |

## Best Practices

### Input Consistency
* Ensure IDs in generation and folding PDBs match for correct evaluation.
* Use the exact score output from Alphafold Initial Guess for accurate merging.

### Workflow Integration
* Place this node after Alphafold Initial Guess in evaluation pipelines.
* Use the output scores for downstream analysis or selection.

## Troubleshooting

### Common Issues
* **Mismatched IDs**: Ensure input PDBs and score tables use consistent IDs.
* **Missing score table**: Always connect the score output from Alphafold Initial Guess.

### Need Help?
* Contact support for further assistance.
