# FASTA Combiner

Combine multiple FASTA sequences into a single FASTA input for downstream processing.

<img src="/images/nodes/biotech/biotech-utils/fasta-combiner.png" alt="FASTA Combiner" class="rounded-lg">

## Quick Start

1. Connect up to five FASTA inputs to the node.
2. Enable the separator option to add comments between combined sequences (optional).
3. Use the output as a single FASTA input for other nodes.

## Setup Guide

### 1. Add the Node
1. Insert the FASTA Combiner node into your workflow.
2. Connect FASTA outputs from upstream nodes to the available inputs.

### 2. Configure Options
1. Optionally enable the separator to add comments between combined FASTA blocks.
2. Leave unused FASTA inputs empty if combining fewer than five sequences.

## Basic Usage

### Combine Multiple FASTA Sequences
* Merge up to five FASTA inputs into a single FASTA string.
* Optionally insert separator comments for clarity.
* Use the combined output as input for downstream sequence analysis or modeling nodes.

## Configuration

### Required Inputs
| Field    | Description                        | Type  | Example         |
|----------|------------------------------------|-------|----------------|
| *None*   |                                    |       |                |

### Optional Inputs
| Field     | Description                                      | Type   | Example         |
|-----------|--------------------------------------------------|--------|----------------|
| fasta_1   | First FASTA sequence(s)                          | FASTA  | >A\nMKT...      |
| fasta_2   | Second FASTA sequence(s)                         | FASTA  | >B\nGYL...      |
| fasta_3   | Third FASTA sequence(s)                          | FASTA  | >C\nLIA...      |
| fasta_4   | Fourth FASTA sequence(s)                         | FASTA  | >D\nNSS...      |
| fasta_5   | Fifth FASTA sequence(s)                          | FASTA  | >E\nGTA...      |
| separator | Add separator comments between different inputs   | BOOL   | true           |

### Outputs
| Field          | Description                        | Type  | Example         |
|----------------|------------------------------------|-------|----------------|
| combined_fasta | Combined FASTA string from inputs  | FASTA | >A\nMKT...      |

## Best Practices

### Input Management
* Only connect FASTA inputs you need; leave others empty.
* Use the separator option to clarify boundaries between combined sequences, especially when merging unrelated data.

### Downstream Compatibility
* Ensure the combined FASTA is compatible with downstream nodes expecting a single FASTA input.
* Validate the output if combining sequences from different sources to avoid ID conflicts.

## Troubleshooting

### Common Issues
* **No output generated**: Ensure at least one FASTA input is provided.
* **Unexpected sequence order**: Inputs are combined in order (fasta_1 to fasta_5); check connections.
* **Downstream errors**: Confirm the combined FASTA format matches requirements of subsequent nodes.

### Need Help?
* Refer to the [SaltAI documentation](https://docs.salt.ai/) for workflow examples.
* For technical support, contact the SaltAI team or visit the user forum.
