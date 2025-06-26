# Boltz-2 Protein Structure Prediction

Predicts 3D protein structures from MSA (A3M) and FASTA using the Boltz-2 model. Supports batch prediction, confidence scoring, and reproducible sampling.

<img src="/images/nodes/biotech/boltz-2/boltz-2-protein-structure-prediction.png" alt="Boltz-2 Protein Structure Prediction" class="rounded-lg">

## Quick Start

1. Prepare your protein FASTA sequence and MSA (A3M format).
2. Set the desired number of structure samples and recycling steps.
3. Run the node to generate ranked structure predictions and confidence scores.

## Setup Guide

### 1. Prepare Inputs
1. Obtain the target protein's FASTA sequence.
2. Run MSA search to generate A3M input (e.g., with MSA Search node).

### 2. Configure Prediction
1. Set `recycling_steps` for quality vs. speed.
2. Choose `diffusion_samples` for diversity.
3. Optionally adjust `sampling_steps`, `use_potentials`, and `output_format`.

### 3. Run and Retrieve Results
1. Execute the node.
2. Download predicted structures and confidence scores from outputs.

## Basic Usage

### Single Protein Structure Prediction
* Input a single FASTA and A3M to predict structure.
* Adjust `recycling_steps` for accuracy.
* Use `diffusion_samples` >1 for diverse predictions.

### Batch Prediction
* Provide A3M with multiple entries to predict structures for each sequence.
* Each output is indexed by input sequence ID.

## Configuration

### Required Inputs
| Field   | Description                        | Type  | Example         |
|---------|------------------------------------|-------|-----------------|
| fasta   | Original protein FASTA sequence    | FASTA | ">A\nMKT..."    |
| a3m     | MSA search results (A3M format)    | A3M   | {"A": "..."}    |
| seed    | Random seed for reproducibility    | INT   | 42              |

### Optional Inputs
| Field           | Description                                 | Type    | Example   |
|-----------------|---------------------------------------------|---------|-----------|
| recycling_steps | Number of recycling iterations              | INT     | 3         |
| diffusion_samples| Number of structure samples to generate     | INT     | 5         |
| sampling_steps  | Steps for diffusion process                 | INT     | 200       |
| use_potentials  | Use potentials for higher quality           | BOOLEAN | True      |
| output_format   | Output file format ("pdb" or "mmcif")      | STRING  | "pdb"     |

### Outputs
| Field            | Description                                 | Example                |
|------------------|---------------------------------------------|------------------------|
| structures.pdb   | Ranked predicted structures (dict)          | {"A_rank_0": "..."}   |
| confidence.json  | Confidence scores for each prediction       | {"A_rank_0": 0.92}    |

## Best Practices

### Input Preparation
* Use high-quality, full-length FASTA sequences.
* Generate A3M using comprehensive MSA search for best results.

### Parameter Tuning
* Increase `recycling_steps` for higher accuracy (at cost of speed).
* Use `diffusion_samples` >1 to explore structural diversity.

## Troubleshooting

### Common Issues
* **Missing or empty FASTA/A3M**: Ensure both inputs are provided and correctly formatted.
* **Low confidence scores**: Try increasing `recycling_steps` or improving MSA quality.
* **Output is empty**: Check input sequence IDs and formats.

### Need Help?
* [Boltz-2 Model Documentation](https://github.com/salt-ai/boltz2)
* [SaltAI Community Support](https://community.salt.ai)
