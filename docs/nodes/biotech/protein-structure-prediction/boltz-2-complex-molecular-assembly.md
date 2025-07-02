# Boltz-2 Complex Molecular Assembly

Boltz-2 Complex Molecular Assembly enables the joint prediction and assembly of multi-component biomolecular complexes, including proteins, DNA, RNA, and ligands, with optional constraints and affinity prediction.

<img src="/images/nodes/biotech/protein-structure-prediction/boltz-2-complex-molecular-assembly.png" alt="Boltz-2 Complex Molecular Assembly" class="rounded-lg">

## Quick Start

1. Prepare FASTA sequences for each molecular component (protein, DNA, RNA) as needed.
2. (Optional) Provide ligand SMILES or CCD codes, and custom MSAs for proteins.
3. (Optional) Specify binding or contact constraints.
4. Set prediction parameters (e.g., recycling steps, diffusion samples).
5. Run the node to generate assembled complex structures and confidence/affinity scores.

## Setup Guide

### 1. Prepare Molecular Inputs
1. Collect and format protein, DNA, RNA sequences in FASTA format.
2. List ligand SMILES or CCD codes (one per line).
3. (Optional) Prepare A3M MSAs for proteins.

### 2. Configure Assembly and Constraints
1. (Optional) Define binding or contact constraints as described in the node UI.
2. (Optional) Enable affinity prediction and specify binder chain if needed.
3. Adjust prediction parameters (recycling steps, diffusion samples, etc.).

## Basic Usage

### Multi-Component Assembly
* Assemble complexes from any combination of proteins, DNA, RNA, and ligands.
* Optionally provide MSAs for improved protein modeling.
* Apply binding/contact constraints to guide assembly.
* Predict binding affinity for ligand-containing complexes.

## Configuration

### Required Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| seed | Random seed for reproducibility | INT | 42 |

### Optional Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| protein_sequences | Protein sequences in FASTA format | FASTA | >A\nMKVL...\n>B\nGYLL... |
| dna_sequences | DNA sequences in FASTA format | FASTA | >DNA1\nATCG... |
| rna_sequences | RNA sequences in FASTA format | FASTA | >RNA1\nAUCGA... |
| ligand_smiles | Ligand SMILES strings (one per line) | STRING | CCO\nCC(=O)O |
| ligand_ccd_codes | Ligand CCD codes (one per line) | STRING | ATP\nGTP |
| protein_msas | Custom MSAs for proteins | A3M | {"A": "..."} |
| binding_constraints | Binding constraints (see UI) | STRING | A:B:10,20 |
| contact_constraints | Contact constraints (see UI) | STRING | A:10-B:20 |
| predict_affinity | Enable binding affinity prediction | BOOLEAN | True |
| affinity_binder_chain | Chain ID for affinity calculation | STRING | L1 |
| recycling_steps | Number of recycling iterations | INT | 3 |
| diffusion_samples | Number of structure samples | INT | 1 |
| use_potentials | Use potentials for improved accuracy | BOOLEAN | False |
| output_format | Output file format | COMBO | pdb |

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| structures.pdb | Assembled complex structures (ranked) | {"A": "...PDB..."} |
| confidence.json | Confidence scores for predictions | {"A": 0.92} |
| affinity.json | Binding affinity scores (if enabled) | {"A": -7.5} |

## Best Practices

### Input Preparation
* Provide high-quality, correctly formatted FASTA sequences for all components.
* Supply MSAs for proteins when possible to improve structure accuracy.

### Constraints & Affinity
* Use binding/contact constraints to guide specific interactions.
* Enable affinity prediction only when ligands are present and specify the correct binder chain.

## Troubleshooting

### Common Issues
* **Missing molecular components**: At least one protein, DNA, RNA, or ligand must be provided.
* **MSA not provided**: Results may be lower quality for proteins without MSAs.
* **Affinity prediction error**: Ensure at least one ligand is present and binder chain is specified or auto-detectable.

### Need Help?
* Contact support for further assistance.
