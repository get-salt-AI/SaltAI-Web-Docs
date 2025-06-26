# PDB To Fasta

Extracts FASTA-formatted amino acid sequences from the provided PDB record, supporting extraction for specific chains or all chains in the structure.

<img src="/images/nodes/biotech/biotech-utils/pdb-to-fasta.png" alt="PDB To Fasta" class="rounded-lg">

## Quick Start

1. Connect a PDB input (single or batch) to the node.
2. Specify chain IDs as a comma-separated string (e.g., `A,B`), or leave empty to extract all chains.
3. Run the node to obtain FASTA sequences for each chain.

## Setup Guide

### 1. Prepare Input
1. Ensure your PDB input is a valid dictionary `{pdb_id: pdb_content}`.
2. Identify the chain(s) you wish to extract (e.g., `A`, `B`, or leave empty for all).

### 2. Node Configuration
1. Set the `chains` field to the desired chain IDs (comma-separated), or leave empty for all chains.
2. Connect the node in your workflow.

## Basic Usage

### Extracting FASTA from a Single Chain
* Provide a PDB with a single chain and specify its ID in `chains` (e.g., `A`).
* The output will be a FASTA record for that chain.

### Extracting FASTA from Multiple Chains
* Provide a PDB with multiple chains and specify multiple IDs (e.g., `A,B`).
* The output will include a FASTA record for each specified chain.

## Configuration

### Required Inputs
| Field  | Description                                      | Type   | Example |
|--------|--------------------------------------------------|--------|---------|
| pdb    | Protein to extract FASTA from.                   | PDB    | {"1abc": "...PDB content..."} |
| chains | Comma-separated chain IDs, or empty for all.     | STRING | A,B     |

### Optional Inputs
*None*

### Outputs
| Field        | Description                                 | Example                |
|--------------|---------------------------------------------|------------------------|
| seqs.fasta   | FASTA records (sequence per chain) extracted from PDB. | >1abc_chain_A\nMKT... |

## Best Practices

### Input Preparation
* Ensure PDB input is properly formatted and contains valid chain IDs.
* Use unique PDB IDs to avoid confusion in batch processing.

### Chain Selection
* Specify only the chains you need to minimize unnecessary output.
* Leave `chains` empty to extract all available chains if unsure.

## Troubleshooting

### Common Issues
* **No valid protein sequences found in the PDB**: Ensure the PDB contains standard amino acid residues and correct chain IDs.
* **Empty output**: Check that the specified chains exist in the input PDB.

### Need Help?
* [PDB Format Guide](https://www.wwpdb.org/documentation/file-format)
* [FASTA Format Reference](https://en.wikipedia.org/wiki/FASTA_format)
