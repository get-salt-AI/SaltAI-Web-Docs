# PDB Visualization

Generates an HTML string for interactive protein structure visualization from a PDB input. Designed for previewing single PDB records; if a batch is provided, only the first (alphabetically) is visualized.

<img src="/images/nodes/biotech/biotech-utils/pdb-visualization.png" alt="PDB Visualization" class="rounded-lg">

## Quick Start

1. Connect a node outputting a `PDB` to the PDB Visualization node.
2. Run the workflow.
3. Use the Output node to preview the protein structure interactively.

## Setup Guide

### 1. Add the Node
1. Insert the PDB Visualization node into your workflow.
2. Connect a `PDB` output from any compatible node.

### 2. Preview the Structure
1. Connect the output of this node to an Output node.
2. Run the workflow to view the protein in an embedded viewer.

## Basic Usage

### Visualizing a Protein Structure
* Use for quick inspection of a single PDB structure.
* If a batch is provided, only the first record is shown.
* Suitable for integration in protein design and analysis pipelines.

## Configuration

### Required Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| pdb   | PDB to visualize. If a batch, only the first is shown. | PDB  | `{ "1abc": "...PDB content..." }` |

### Optional Inputs
*None*

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| html  | HTML string for protein preview. | `<!DOCTYPE html>...` |

## Best Practices

### Visualization
* Use for single-structure visualization; batch support is not available.
* Connect directly to an Output node for immediate preview.

### Data Integrity
* Ensure the PDB input is valid and contains at least one record.
* For batch PDBs, verify the desired structure is first alphabetically if previewing a specific one.

## Troubleshooting

### Common Issues
* **No structure displayed**: Ensure the input PDB is valid and not empty.
* **Batch input not fully visualized**: Only the first PDB in a batch is shown; split batches if needed.

### Need Help?
* [Mol* Viewer Documentation](https://molstar.org/viewer/)
* [SaltAI Support](https://support.salt.ai)
