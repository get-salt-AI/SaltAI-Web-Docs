# Batch PDB

Merges multiple PDB dictionaries into a single batch, ensuring all PDB IDs are unique. Useful for combining single or batched PDB inputs for downstream processing.

<img src="/images/nodes/biotech/biotech-utils/batch-pdb.png" alt="Batch PDB" class="rounded-lg">

## Quick Start

1. Connect at least one PDB input to `pdb_1`.
2. Add more PDBs by connecting to `pdb_2`, `pdb_3`, etc. (up to 30 inputs).
3. Use the output batch in downstream nodes.

## Setup Guide

### 1. Prepare PDB Inputs
1. Generate or load PDBs using upstream nodes (e.g., Load PDB).
2. Ensure each PDB has a unique ID.

### 2. Connect Inputs
1. Connect each PDB to a separate input slot (`pdb_1`, `pdb_2`, ...).
2. Additional slots appear as you connect more PDBs.

## Basic Usage

### Batch Multiple PDBs
* Combine single or batched PDB dictionaries into one batch.
* Automatically checks for unique IDs to prevent conflicts.
* Supports up to 31 PDB inputs.

## Configuration

### Required Inputs
| Field  | Description                                                                 | Type | Example |
|--------|-----------------------------------------------------------------------------|------|---------|
| pdb_1  | PDB to add to batch (single or batch). `pdb_2` appears after connecting `pdb_1`. | PDB  | {"1abc": "...PDB..."} |

### Optional Inputs
| Field  | Description                                                                 | Type | Example |
|--------|-----------------------------------------------------------------------------|------|---------|
| pdb_2+ | Additional PDBs to add to batch. Each slot appears after the previous is set. | PDB  | {"2def": "...PDB..."} |

### Outputs
| Field          | Description                                      | Type | Example |
|----------------|--------------------------------------------------|------|---------|
| structure.pdb  | Batch of PDBs as a dict `{pdb_id: pdb_content}`. | PDB  | {"1abc": "...", "2def": "..."} |

## Best Practices

### Input Preparation
* Ensure all PDB IDs are unique to avoid errors.
* Use upstream nodes that assign or allow setting PDB IDs.

### Workflow Integration
* Use Batch PDB to merge results before batch processing or evaluation.
* Connect only the PDBs you need for the batch to avoid unnecessary data.

## Troubleshooting

### Common Issues
* **Duplicate PDB IDs**: The node will raise an error if any PDB IDs are repeated. Ensure all input PDBs have unique IDs.
* **Missing Inputs**: At least one PDB input (`pdb_1`) is required.

### Need Help?
* [SaltAI Documentation](https://docs.salt.ai/)
* [Contact Support](mailto:support@salt.ai)
