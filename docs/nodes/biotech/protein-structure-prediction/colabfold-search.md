# Colabfold Search

> This node is currently disabled.
>
> Need access? Contact us and we’ll help you out. 

Performs Colabfold MSA search on provided protein sequences against known sequence databases, returning results for each sequence in `.a3m` format as a dictionary `{seq_id: a3m_content}`.

<img src="/images/nodes/biotech/protein-structure-prediction/colabfold-search.png" alt="Colabfold Search" class="rounded-lg">

## Quick Start

1. Prepare your protein sequences in FASTA format.
2. Connect the FASTA input to the Colabfold Search node.
3. Run the node to obtain MSA search results in `.a3m` format.

## Setup Guide

### 1. Prepare Input
1. Format your protein sequences as a valid FASTA string.
2. Ensure each sequence has a unique identifier.

### 2. Run Colabfold Search
1. Connect the FASTA input to the node.
2. Execute the node to perform the MSA search.

## Basic Usage

### Single or Batch MSA Search
* Accepts single or multiple protein sequences in FASTA format.
* Returns a dictionary mapping each sequence ID to its `.a3m` MSA result.

## Configuration

### Required Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| fasta | Sequences to run Colabfold MSA search on. | FASTA | ">seq1\nMKT..." |

### Optional Inputs
*None*

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| msa.a3m | Search results as a dict `{seq_id: a3m_content}`. | `{ "seq1": "...a3m..." }` |

## Best Practices

### Input Preparation
* Use clear, unique sequence IDs in your FASTA records.
* Validate FASTA formatting to avoid processing errors.

### Workflow Integration
* Use the output `.a3m` directly as input for downstream structure prediction nodes (e.g., Alphafold, Boltz).
* For batch processing, ensure all sequences are relevant to the same analysis context.

## Troubleshooting

### Common Issues
* **Empty or malformed FASTA input**: Ensure your input is properly formatted and not empty.
* **Timeouts on large batches**: For very large numbers of sequences, consider splitting into smaller batches.

### Need Help?
* [ColabFold documentation](https://colabfold.com/)
* Contact support for further assistance.
