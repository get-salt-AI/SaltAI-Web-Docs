# Prodigy

Predicts the binding affinity of protein-protein complexes using structural and interface analysis.

<img src="/images/nodes/biotech/functional-prediction/prodigy.png" alt="Prodigy" class="rounded-lg">

## Quick Start

1. Prepare a PDB file of your protein complex.
2. Specify the chains to analyze (e.g., `A;B` for two molecules).
3. Set temperature and optional thresholds as needed.
4. Run the node to obtain binding affinity metrics.

## Setup Guide

### 1. Input Preparation
1. Ensure your protein complex is in PDB format.
2. Identify chain IDs for each molecule or group.

### 2. Node Configuration
1. Enter the PDB structure and chain selection string.
2. Adjust temperature, distance cutoff, and accessibility threshold as required.

## Basic Usage

### Binding Affinity Prediction
* Predicts binding affinity (ΔG, Kd) for protein complexes.
* Supports custom chain grouping for multi-chain complexes.
* Outputs a CSV table with detailed interface metrics.

## Configuration

### Required Inputs
| Field         | Description                                                                 | Type   | Example         |
|--------------|-----------------------------------------------------------------------------|--------|----------------|
| pdb          | Target protein complex to predict binding affinity for.                     | PDB    | complex.pdb    |
| chains       | Chains to consider; semicolon separates molecules, comma groups chains.     | STRING | A;B;C or A,B;C |
| temperature  | Temperature (°C) for Kd prediction.                                        | INT    | 25             |
| distance_cutoff | Distance cutoff (Å) to calculate interface contacts.                     | FLOAT  | 5.5            |
| acc_threshold   | Accessibility threshold for BSA analysis.                                | FLOAT  | 0.05           |

### Optional Inputs
*None*

### Outputs
| Field     | Description                                                                                  | Example         |
|-----------|----------------------------------------------------------------------------------------------|-----------------|
| score.csv | Table with binding scores (binding_affinity, kd, contacts, contact_types, nis_a, nis_c, temperature) | score.csv       |

## Best Practices

### Chain Selection
* Use semicolons to separate different molecules (e.g., `A;B`).
* Use commas to group chains as a single molecule (e.g., `A,B;C`).

### Parameter Tuning
* Adjust `distance_cutoff` and `acc_threshold` for specific interface definitions.
* Set temperature to match experimental or physiological conditions.

## Troubleshooting

### Common Issues
* **Incorrect chain selection**: Double-check chain IDs and grouping format.
* **Empty or invalid PDB input**: Ensure the PDB file is valid and contains the specified chains.

### Need Help?
* [Prodigy web server documentation](https://bianca.science.uu.nl/prodigy/)
* [SaltAI documentation](https://docs.salt.ai/)
