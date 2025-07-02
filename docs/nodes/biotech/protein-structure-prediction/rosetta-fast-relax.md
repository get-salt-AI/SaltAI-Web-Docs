# Rosetta Fast Relax

Runs ProteinMPNN-FastRelax cycles on sequences and structures to make proteins more stable and realistic.

<!-- <img src="/images/nodes/biotech/protein-structure-prediction/rosetta-fast-relax.png" alt="Rosetta Fast Relax" class="rounded-lg"> -->

## Quick Start

1. Prepare your input FASTA and PDB files with matching IDs.
2. Set the desired number of relax cycles.
3. Run the node to obtain relaxed protein structures.

## Setup Guide

### 1. Prepare Input Data
1. Ensure your FASTA contains sequence records with IDs matching the PDB structure IDs.
2. Ensure your PDB input contains structures with IDs matching the FASTA records.

### 2. Configure Node Parameters
1. Set the number of relax cycles (`relax_cycles`).
2. Choose whether to enable debug mode (`debug`).

## Basic Usage

### Relaxing Protein Structures
* Use to improve stability and realism of predicted protein structures.
* Batch process multiple sequence-structure pairs by matching IDs.
* Useful after structure prediction or design steps.

## Configuration

### Required Inputs
| Field         | Description                                                                 | Type    | Example |
|--------------|-----------------------------------------------------------------------------|---------|---------|
| fasta        | Sequences to run through the model. IDs must match structure IDs.           | FASTA   | ">design_1\nMKT..." |
| pdb          | Structures to run through the model. IDs must match sequence IDs.           | PDB     | "{design_1: ...}" |
| relax_cycles | Number of relax cycles to perform on each structure.                        | INT     | 3       |
| debug        | If true, errors will crash the script; if false, will skip failed poses.    | BOOLEAN | true    |

### Optional Inputs
*None*

### Outputs
| Field         | Description           | Type | Example |
|---------------|-----------------------|------|---------|
| structure.pdb | Relaxed structures.   | PDB  | "{design_1: ...}" |

## Best Practices

### Input Preparation
* Ensure FASTA and PDB IDs match exactly for correct pairing.
* Use validated, high-quality input structures for best results.

### Parameter Tuning
* Start with a low number of relax cycles for speed; increase for higher accuracy.
* Enable debug mode during development to catch errors early.

## Troubleshooting

### Common Issues
* **Mismatched IDs**: Ensure FASTA and PDB record IDs correspond exactly.
* **Node crashes on error**: Set `debug` to false to skip failed poses instead of crashing.

### Need Help?
* [Rosetta Fast Relax Paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2849174/)
* Contact support for further assistance.
