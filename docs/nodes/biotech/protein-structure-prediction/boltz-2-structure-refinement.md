# Boltz-2 Structure Refinement

Refines protein structures using Boltz-2 with inference-time potentials for improved accuracy and quality.

<!-- <img src="/images/nodes/biotech/protein-structure-prediction/boltz-2-structure-refinement.png" alt="Boltz-2 Structure Refinement" class="rounded-lg"> -->

## Quick Start

1. Provide a PDB structure as input.
2. Set the desired random seed and optional refinement parameters.
3. Run the node to generate refined structures and confidence scores.

## Setup Guide

### 1. Prepare Input
1. Obtain or generate a PDB structure to refine.
2. Ensure the structure is in the correct PDB dictionary format.

### 2. Configure Node
1. Set the `seed` for reproducibility (integer).
2. Optionally adjust recycling steps, number of samples, and potentials usage.

### 3. Run Refinement
1. Execute the node to receive refined structures and confidence metrics.

## Basic Usage

### Refining a Predicted Structure
* Use after initial structure prediction to improve model quality.
* Supports batch refinement by increasing `diffusion_samples`.
* Recommended to keep `use_potentials` enabled for best results.

## Configuration

### Required Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| pdb | Input PDB structure to refine | PDB | {"design_0": "...PDB content..."} |
| seed | Random seed for reproducibility | INT | 42 |

### Optional Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| recycling_steps | Number of recycling iterations (higher = better quality) | INT | 3 |
| diffusion_samples | Number of refined structures to generate | INT | 3 |
| use_potentials | Use inference-time potentials (recommended) | BOOLEAN | True |
| output_format | Output file format | COMBO ("pdb", "mmcif") | "pdb" |

### Outputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| refined_structures.pdb | Refined structure(s) as a dict | PDB | {"design_0_ranked_0": "...PDB..."} |
| confidence.json | Confidence scores for each refined structure | JSON | {"design_0_ranked_0": 0.92} |

## Best Practices

### Refinement Settings
* Enable `use_potentials` for improved physical plausibility.
* Increase `recycling_steps` for higher accuracy if runtime allows.

### Batch Processing
* Use higher `diffusion_samples` to explore multiple refined conformations.
* Review confidence scores to select the best refined model.

## Troubleshooting

### Common Issues
* **Input format error**: Ensure the PDB input is a dictionary with valid PDB content.
* **No output generated**: Check that all required fields are provided and input is not empty.

### Need Help?
* Contact support for further assistance.
