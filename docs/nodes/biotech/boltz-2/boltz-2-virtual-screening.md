# Boltz-2 Virtual Screening

Performs high-throughput virtual screening of small molecules against a target protein using Boltz-2, predicting binding affinity and ranking top candidates for drug discovery.

<img src="/images/nodes/biotech/boltz-2/boltz-2-virtual-screening.png" alt="Boltz-2 Virtual Screening" class="rounded-lg">

## Quick Start

1. Prepare the target protein FASTA sequence.
2. List ligand SMILES strings (one per line).
3. (Recommended) Provide a protein MSA (A3M format) for better accuracy.
4. Set screening parameters as needed.
5. Run the node to obtain ranked screening results.

## Setup Guide

### 1. Prepare Inputs
1. Obtain the target protein FASTA sequence.
2. Collect ligand SMILES strings (one per line).
3. (Optional) Prepare ligand names and protein MSA (A3M).

### 2. Configure Screening
1. Adjust parameters such as top_k, affinity threshold, and batch size as needed.
2. Enable or disable fast mode for speed/accuracy tradeoff.

### 3. Run and Review Results
1. Execute the node.
2. Review the output JSON for ranked hits and predicted affinities.

## Basic Usage

### Virtual Screening Workflow
* Screen a library of small molecules against a protein target.
* Filter hits by predicted affinity and binding probability.
* Retrieve top-ranked ligands for further analysis.

## Configuration

### Required Inputs
| Field            | Description                          | Type   | Example                |
|------------------|--------------------------------------|--------|------------------------|
| protein_fasta    | Target protein FASTA sequence        | FASTA  | ">A\nMKT..."           |
| ligand_smiles_list | SMILES strings (one per line)      | STRING | "CCO\nC1=CC=CC=C1"     |
| seed             | Random seed for reproducibility      | INT    | 42                     |

### Optional Inputs
| Field                  | Description                                      | Type   | Example                |
|------------------------|--------------------------------------------------|--------|------------------------|
| top_k                  | Number of top hits to return                     | INT    | 10                     |
| affinity_threshold     | Affinity cutoff (log IC50)                       | FLOAT  | -5.0                   |
| binding_prob_threshold | Binding probability cutoff                       | FLOAT  | 0.7                    |
| ligand_names           | Ligand names (one per line, matches SMILES)      | STRING | "Aspirin\nIbuprofen"   |
| protein_msa            | Protein MSA (A3M format, recommended)            | A3M    | {"A": "..."}           |
| batch_size             | Number of ligands processed per batch            | INT    | 5                      |
| fast_mode              | Use faster settings for screening                | BOOLEAN| True                   |

### Outputs
| Field                  | Description                                      | Example                |
|------------------------|--------------------------------------------------|------------------------|
| screening_results.json | Ranked screening results with affinities, scores | {"hits": [...]}        |

## Best Practices

### Input Preparation
* Always provide a high-quality protein FASTA sequence.
* Use realistic, valid SMILES strings for ligands.
* Supplying a protein MSA (A3M) significantly improves prediction quality.

### Screening Parameters
* Adjust `top_k` and thresholds to balance hit rate and specificity.
* Use `fast_mode` for rapid initial screening; disable for higher accuracy.

## Troubleshooting

### Common Issues
* **Missing or invalid FASTA/SMILES**: Ensure all required fields are filled and formatted correctly.
* **No hits returned**: Loosen affinity or probability thresholds, or check input quality.
* **Mismatch in ligand names and SMILES**: Provide the same number of names as SMILES entries.

### Need Help?
* Contact support for further assistance.
