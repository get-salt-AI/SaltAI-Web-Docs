# Alphafold Initial Guess

Predicts binder structure from sequence and an initial guess structure, initializing Alphafold with binder atoms to ensure proper binding during folding.

<!-- <img src="/images/nodes/biotech/protein-structure-prediction/alphafold-initial-guess.png" alt="Alphafold Initial Guess" class="rounded-lg"> -->

## Quick Start

1. Prepare matching FASTA and PDB files (IDs must correspond).
2. Set desired parameters (e.g., recycles, monomer mode).
3. Run the node to generate predicted foldings and scores.

## Setup Guide

### 1. Prepare Inputs
1. Ensure FASTA and PDB records have matching IDs for batch processing.
2. Adjust sequence and structure as needed for your design.

### 2. Configure Node
1. Set `maintain_res_numbering` to preserve residue numbering if required.
2. Adjust `max_amide_dist`, `recycle`, and `force_monomer` as needed.
3. Enable `debug` for strict error handling.

## Basic Usage

### Single or Batch Prediction
* Use for single or multiple sequence/structure pairs.
* Useful for binder design and initial structure refinement.

## Configuration

### Required Inputs
| Field                  | Description                                                                 | Type    | Example   |
|------------------------|-----------------------------------------------------------------------------|---------|-----------|
| fasta                  | Sequences to run through the model. IDs must match PDBs for batch.          | FASTA   | ">id1\nSEQUENCE" |
| pdb                    | Structures to run through the model. IDs must match FASTA for batch.        | PDB     | "{id1: pdb_content}" |
| maintain_res_numbering | Do not renumber residues on bad input.                                      | BOOLEAN | False     |
| max_amide_dist         | Max distance between amide bond's carbon and nitrogen.                      | FLOAT   | 3.0       |
| recycle                | Number of AF2 recycles to perform.                                          | INT     | 3         |
| force_monomer          | Predict structure as monomer.                                               | BOOLEAN | False     |
| debug                  | Crash on errors if True, continue otherwise.                                | BOOLEAN | True      |

### Optional Inputs
*None*

### Outputs
| Field         | Description                                 | Example                |
|---------------|---------------------------------------------|------------------------|
| folding.pdb   | Predicted foldings.                         | "{id1: pdb_content}"   |
| score.sc      | Scores of the designs (rmsd, pae, plddt).   | "rmsd: 1.2, plddt: 85" |

## Best Practices

### Input Preparation
* Ensure FASTA and PDB IDs match for batch processing.
* Use high-quality initial structures for better results.

### Parameter Tuning
* Adjust `recycle` for accuracy vs. speed.
* Use `force_monomer` for monomeric predictions only.

## Troubleshooting

### Common Issues
* **Mismatched IDs**: Ensure FASTA and PDB record IDs correspond exactly.
* **Unexpected output**: Check input formats and parameter values.

### Need Help?
* Contact support for further assistance.
