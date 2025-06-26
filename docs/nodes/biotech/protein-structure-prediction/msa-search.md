# MSA Search

Performs MSA search on provided protein sequences against known sequence databases, returning results in unified `.a3m` format for each input sequence.

<img src="/images/nodes/biotech/protein-structure-prediction/msa-search.png" alt="MSA Search" class="rounded-lg">

## Quick Start

1. Prepare your input FASTA file with one or more protein sequences.
2. Select the desired database preset (Toy, Reduced, or Full).
3. Run the node to obtain MSA results in `.a3m` format for each sequence.

## Setup Guide

### 1. Prepare Input Data
1. Format your protein sequences in standard FASTA format.
2. Ensure each sequence has a unique identifier.

### 2. Configure Node Parameters
1. Choose the database preset according to your speed and accuracy needs.
2. Set the mode (PROD for full search, MOCK for testing, TEST for quick checks).

## Basic Usage

### Single or Batch MSA Search
* Accepts single or multiple sequences in FASTA format.
* Returns a dictionary mapping each sequence ID to its `.a3m` MSA result.
* Supports quick testing with toy datasets for rapid iteration.

## Configuration

### Required Inputs
| Field      | Description                                 | Type   | Example                |
|------------|---------------------------------------------|--------|------------------------|
| fasta      | Sequences to run MSA search on.             | FASTA  | ">seq1\nMKT..."        |
| db_preset  | Dataset to perform MSA search on.           | COMBO  | "alphafold_reduced_dataset" |
| mode       | Mode to run the node in.                    | COMBO  | "PROD"                 |

### Optional Inputs
*None*

### Outputs
| Field    | Description                                 | Example                |
|----------|---------------------------------------------|------------------------|
| msa.a3m  | Search results as a dict {seq_id: a3m_content}. | {"seq1": "...a3m..."}   |

## Best Practices

### Efficient Testing
* Use `MOCK` or `TEST` mode for rapid validation and debugging.
* Start with the Toy dataset to ensure pipeline connectivity before running full searches.

### Database Selection
* Use the Reduced or Full database presets for production runs to maximize search coverage.
* Choose the Toy preset for development or when working with short sequences.

## Troubleshooting

### Common Issues
* **Long runtime:** Full database searches can take significant time; use Toy or Reduced presets for faster results.
* **Empty output:** Ensure input FASTA is correctly formatted and contains valid sequences.
* **Mode confusion:** If results look unexpected, verify the selected mode (MOCK, PROD, TEST).

### Need Help?
* [MSA search documentation](https://alphafold.ebi.ac.uk/)
* [FASTA format guide](https://en.wikipedia.org/wiki/FASTA_format)
