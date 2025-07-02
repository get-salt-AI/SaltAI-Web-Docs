# Boltz-2 Protein-Ligand Complex Prediction

Predicts the structure and binding affinity of protein-ligand complexes using the Boltz-2 model. Supports both separate protein/ligand inputs and pre-formed complex PDBs.

<!-- <img src="/images/nodes/biotech/protein-structure-prediction/boltz-2-protein-ligand-complex-prediction.png" alt="Boltz-2 Protein-Ligand Complex Prediction" class="rounded-lg"> -->

## Quick Start

1. Select input mode: separate components or complex PDB.
2. Provide protein (A3M or FASTA) and ligand (SMILES or CCD) or upload a complex PDB.
3. Set prediction parameters as needed.
4. Run the node to obtain predicted structures, confidence, and affinity scores.

## Setup Guide

### 1. Prepare Inputs
1. Choose input mode: `separate_components` (default) or `complex_pdb`.
2. For `separate_components`, provide:
   - Protein sequence (A3M or FASTA)
   - Ligand (SMILES string or CCD code)
3. For `complex_pdb`, upload a pre-formed protein-ligand complex PDB.

### 2. Configure Prediction
1. Adjust optional parameters (recycling steps, diffusion samples, output format, etc.) as needed.
2. Enable or disable binding affinity prediction.

## Basic Usage

### Predicting Protein-Ligand Complex Structure
* Use `separate_components` mode for de novo prediction from sequence/MSA and ligand.
* Use `complex_pdb` mode to refine or score an existing complex.
* Adjust `recycling_steps` for quality vs. speed.
* Set `diffusion_samples` >1 for diverse predictions.

## Configuration

### Required Inputs
| Field         | Description                                 | Type   | Example         |
|---------------|---------------------------------------------|--------|-----------------|
| input_mode    | Input mode: separate components or PDB      | STRING | separate_components |
| seed          | Random seed for reproducibility             | INT    | 42              |

### Optional Inputs
| Field              | Description                                         | Type    | Example         |
|--------------------|-----------------------------------------------------|---------|-----------------|
| protein_input_type | Protein input type: a3m or fasta                    | STRING  | a3m             |
| protein_a3m        | Protein MSA (single-entry dict)                     | A3M     | {"A": "..."}   |
| protein_fasta      | Protein FASTA sequence                              | FASTA   | ">A\nMKT..."   |
| ligand_type        | Ligand input type: smiles or ccd                    | STRING  | smiles          |
| ligand_smiles      | Ligand SMILES string                                | STRING  | CCO             |
| ligand_ccd         | Ligand CCD code                                     | STRING  | ATP             |
| complex_pdb        | Pre-formed protein-ligand complex                   | PDB     | {"complex": "..."} |
| recycling_steps    | Number of recycling iterations                      | INT     | 3               |
| diffusion_samples  | Number of structure samples to generate             | INT     | 1               |
| predict_affinity   | Predict binding affinity                            | BOOLEAN | True            |
| use_potentials     | Use potentials for improved quality                 | BOOLEAN | False           |
| output_format      | Output format: pdb or mmcif                         | STRING  | pdb             |

### Outputs
| Field            | Description                                 | Example         |
|------------------|---------------------------------------------|-----------------|
| structures.pdb   | Predicted protein-ligand complex structures | {"A": "..."}   |
| confidence.json  | Confidence scores for predictions           | {"A": 0.92}    |
| affinity.json    | Predicted binding affinity metrics          | {"A": -7.1}    |

## Best Practices

### Input Preparation
* Use high-quality MSA (A3M) for best structure prediction results.
* Provide chemically valid SMILES or correct CCD codes for ligands.

### Parameter Tuning
* Increase `recycling_steps` for higher accuracy (at the cost of speed).
* Use multiple `diffusion_samples` to explore structural diversity.

## Troubleshooting

### Common Issues
* **Missing or invalid input:** Ensure all required fields are provided and correctly formatted.
* **Low confidence or affinity scores:** Try increasing recycling steps or providing better MSA/ligand data.
* **Affinity prediction fails:** Confirm ligand is present and correctly specified.

### Need Help?
* Contact support for further assistance.
