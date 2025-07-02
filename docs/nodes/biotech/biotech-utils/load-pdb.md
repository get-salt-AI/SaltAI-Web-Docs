# Load PDB

Loads PDB content from a string and assigns a unique ID for use in downstream workflows. The output is a dictionary with the PDB ID as the key and the PDB string as the value.

<img src="/images/nodes/biotech/biotech-utils/load-pdb.png" alt="Load PDB" class="rounded-lg">

## Quick Start

1. Enter or paste your PDB string.
2. Specify a unique PDB ID (match with FASTA if needed).
3. Connect the output to downstream nodes.

## Setup Guide

### 1. Prepare PDB Input
1. Obtain or generate your PDB string.
2. Ensure you have a unique identifier for this PDB.

### 2. Node Configuration
1. Input the PDB string in the provided field.
2. Set the PDB ID (default is "pdb").
3. Connect the output to other nodes as required.

## Basic Usage

### Single PDB Loading
* Load a single PDB structure for use in a workflow.
* Assign a unique ID to ensure compatibility with batch and sequence nodes.

## Configuration

### Required Inputs
| Field      | Description                                                                 | Type    | Example   |
|------------|-----------------------------------------------------------------------------|---------|-----------|
| pdb_string | PDB string to upload.                                                       | STRING  | ATOM ...  |
| pdb_id     | ID assigned to this PDB. Should be unique if using multiple PDBs.           | STRING  | 1abc      |

### Optional Inputs
*None*

### Outputs
| Field          | Description                                         | Example                |
|----------------|-----------------------------------------------------|------------------------|
| structure.pdb  | Dictionary with pdb_id as key and pdb_string as value. | {"1abc": "ATOM ..."} |

## Best Practices

### Unique Identification
* Always assign a unique PDB ID, especially when using multiple PDBs in a workflow.
* Match the PDB ID with the corresponding FASTA sequence ID if both are used.

### Data Integrity
* Validate your PDB string for formatting errors before uploading.
* Use descriptive IDs to simplify workflow management.

## Troubleshooting

### Common Issues
* **Duplicate PDB IDs**: Ensure each PDB has a unique ID to avoid downstream errors.
* **Invalid PDB Format**: Check that the PDB string is correctly formatted and complete.

### Need Help?
* Contact support for further assistance.
