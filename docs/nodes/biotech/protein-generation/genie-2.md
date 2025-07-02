# Genie 2

Runs inference of a Genie 2 model for protein generation, supporting both unconditional and motif/multimotif scaffolding. Fully reproducible, with flexible control over sequence length and motif placement.

<!-- <img src="/images/nodes/biotech/protein-generation/genie-2.png" alt="Genie 2" class="rounded-lg"> -->

## Quick Start

1. Select the desired checkpoint (40-epoch for unconditional, 30-epoch for motif scaffolding).
2. Set minimum and maximum sequence lengths.
3. (Optional) Provide input PDB and contigs for motif scaffolding.
4. Run the node to generate protein structures.

## Setup Guide

### 1. Unconditional Generation
1. Leave `input_pdb` and `contigs` empty.
2. Set `min_length`, `max_length`, and `length_step` for desired sequence range.
3. Choose checkpoint (recommended: 40-epoch).

### 2. Motif/Multimotif Scaffolding
1. Provide an `input_pdb` containing the motif(s).
2. Define `contigs` to specify motif and flexible regions (e.g., `A367-372/10-10/A342-348`).
3. Set `min_length` and `max_length` (used for motif scaffolding as well).
4. Choose checkpoint (recommended: 30-epoch).

## Basic Usage

### Unconditional Generation
* Generate proteins of varying lengths without motifs.
* Use for de novo protein design.

### Motif Scaffolding
* Scaffold one or more motifs into generated proteins.
* Specify motif positions and flexible regions using `contigs`.

## Configuration

### Required Inputs
| Field        | Description                                                                                                                        | Type   | Example                                      |
|--------------|------------------------------------------------------------------------------------------------------------------------------------|--------|----------------------------------------------|
| checkpoint   | Checkpoint name. Use 40-epoch for unconditional, 30-epoch for motif scaffolding.                                                   | COMBO  | base/epoch.40                                |
| scale        | Sampling noise scale.                                                                                                              | FLOAT  | 0.6                                          |
| batch_size   | Batch size. Reduce if running out of GPU memory.                                                                                   | INT    | 1                                            |
| min_length   | Minimum sequence length.                                                                                                           | INT    | 100                                          |
| max_length   | Maximum sequence length.                                                                                                           | INT    | 100                                          |
| length_step  | Sequence length step size (unconditional only).                                                                                    | INT    | 5                                            |
| contigs      | Scaffold structure: fixed motifs (A10-25), flexible regions (5-15), explicit motif groups (A, B, etc.). See Setup Guide for format. | STRING | A367-372/10-10/A342-348                      |
| seed         | Base seed for randomness.                                                                                                          | INT    | 42                                           |

### Optional Inputs
| Field     | Description                                      | Type | Example |
|-----------|--------------------------------------------------|------|---------|
| input_pdb | Initial PDB to start generation from (scaffolding) | PDB  | {"motif1": "...PDB..."} |

### Outputs
| Field           | Description              | Example                |
|-----------------|-------------------------|------------------------|
| generation.pdb  | Generation result.      | {"design_0": "..."}  |
| motif.pdb       | Generation motif pdb.   | {"motif1": "..."}    |

## Best Practices

### Checkpoint Selection
* Use 40-epoch checkpoint for unconditional generation.
* Use 30-epoch checkpoint for motif/multimotif scaffolding.

### Contigs Formatting
* For motif scaffolding, clearly define motif and flexible regions in `contigs`.
* Use explicit motif groups (A, B, etc.) for multimotif tasks.

## Troubleshooting

### Common Issues
* **No lengths generated:** Ensure `min_length` ≤ `max_length` and `length_step` is positive.
* **Missing contigs for scaffolding:** For motif scaffolding, both `input_pdb` and `contigs` must be provided.
* **Chain breaks not supported:** All segments in `contigs` are sequentially connected; do not use chain breaks.

### Need Help?
* Contact support for further assistance.
