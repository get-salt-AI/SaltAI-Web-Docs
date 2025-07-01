# A3M Combiner

Combines multiple A3M MSA dictionaries into a single A3M input, supporting up to five sources and optional chain prefixing to avoid ID conflicts.

<img src="/images/nodes/biotech/biotech-utils/a3m-combiner.png" alt="A3M Combiner" class="rounded-lg">

## Quick Start

1. Connect up to five A3M MSA dictionary inputs (e.g., from MSA Search nodes).
2. (Optional) Enable chain prefixing to avoid chain ID conflicts.
3. Use the combined A3M output in downstream structure prediction nodes.

## Setup Guide

### 1. Prepare Inputs
1. Generate A3M MSA dictionaries using MSA Search or similar nodes.
2. Ensure each input is a valid A3M dictionary.

### 2. Configure Node
1. Connect A3M inputs to the corresponding slots (`a3m_1` to `a3m_5`).
2. Enable `prefix_chains` if chain ID conflicts are possible.

### 3. Use Output
1. Connect the `combined_a3m` output to nodes requiring a merged MSA input.

## Basic Usage

### Merging Multiple MSAs
* Combine up to five A3M dictionaries into one.
* Optionally add prefixes to chain IDs to prevent conflicts.
* Use in workflows requiring unified MSA input for multimeric or complex assemblies.

## Configuration

### Required Inputs
*None*

### Optional Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| a3m_1 | First A3M MSA dictionary | A3M | {"A": "..."} |
| a3m_2 | Second A3M MSA dictionary | A3M | {"B": "..."} |
| a3m_3 | Third A3M MSA dictionary | A3M | {"C": "..."} |
| a3m_4 | Fourth A3M MSA dictionary | A3M | {"D": "..."} |
| a3m_5 | Fifth A3M MSA dictionary | A3M | {"E": "..."} |
| prefix_chains | Add prefixes to chain IDs to avoid conflicts | BOOLEAN | True |

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| combined_a3m | Combined A3M MSA dictionary | {"A": "...", "B": "..."} |

## Best Practices

### Avoiding Chain ID Conflicts
* Enable `prefix_chains` when combining MSAs with overlapping chain IDs.
* Review output chain IDs before downstream use.

### Workflow Integration
* Use this node to prepare MSAs for multimeric structure prediction.
* Combine only relevant MSAs to minimize unnecessary data.

## Troubleshooting

### Common Issues
* **Chain ID conflict**: Enable `prefix_chains` to resolve duplicate chain IDs.
* **Empty output**: Ensure at least one valid A3M input is connected.

### Need Help?
* Contact support for further assistance.
