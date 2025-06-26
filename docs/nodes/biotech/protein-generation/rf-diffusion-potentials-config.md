# RF Diffusion Potentials Config

Provides interface for passing potential configuration to the RF Diffusion node, enabling advanced control over guiding potentials and related parameters.

<img src="/images/nodes/biotech/protein-generation/rf-diffusion-potentials-config.png" alt="RF Diffusion Potentials Config" class="rounded-lg">

## Quick Start

1. Add the RF Diffusion Potentials Config node to your workflow.
2. Specify guiding potentials and other parameters as needed.
3. Connect the output to the RF Diffusion node's `potentials_config` input.

## Setup Guide

### 1. Insert Node
1. Drag the RF Diffusion Potentials Config node into your workflow.
2. Connect it to the RF Diffusion node.

### 2. Configure Potentials
1. Define guiding potentials using the provided string format.
2. Adjust scale, decay, and other options as required for your design.

## Basic Usage

### Example Use Cases
* Add monomer or oligomer contact potentials to guide protein generation.
* Fine-tune the influence of potentials with scale and decay settings.
* Specify custom contacts or substrate-based potentials for advanced scenarios.

## Configuration

### Required Inputs
| Field              | Description                                                                                                                        | Type     | Example                                                                                                                        |
|--------------------|------------------------------------------------------------------------------------------------------------------------------------|----------|--------------------------------------------------------------------------------------------------------------------------------|
| guiding_potentials | Semicolon-separated list of potentials. Each: type:potential_type,arg1:val1,arg2:val2,... Supported types and args in tooltip.      | STRING   | type:monomer_ROG,weight:1,min_dist:5;type:olig_contacts,weight_intra:1,weight_inter:0.1                                       |
| guide_scale        | Scale factor for applying guiding potentials.                                                                                      | INT      | 10                                                                                                                             |
| guide_decay        | Decay type for applying guiding potentials.                                                                                        | COMBO    | constant                                                                                                                       |
| olig_inter_all     | Whether all off-diagonal elements have contact potential.                                                                         | BOOLEAN  | False                                                                                                                          |
| olig_intra_all     | Whether all on-diagonal elements have contact potential.                                                                          | BOOLEAN  | False                                                                                                                          |
| olig_custom_contact| Comma-separated contacts between chain pairs (! = repulsive, & = attractive).                                                      | STRING   | A!B,B&C                                                                                                                        |
| substrate          | Ligand residue name in input PDB for substrate-based potentials.                                                                   | STRING   | LLK                                                                                                                            |

### Optional Inputs
*None*

### Outputs
| Field            | Description                                      | Example         |
|------------------|--------------------------------------------------|-----------------|
| potentials_config| Configuration dict for RF Diffusion node.        | { ... }         |

## Best Practices

### Defining Potentials
* Use clear, supported types and argument names for each potential.
* Combine multiple potentials with semicolons for complex guidance.

### Parameter Tuning
* Start with default scale and decay, then adjust for desired effect.
* Use custom contacts and substrate only when required for your design.

## Troubleshooting

### Common Issues
* **Invalid potential format**: Ensure each potential follows the required type:arg1:val1,... format and is separated by semicolons.
* **Unsupported potential type**: Check tooltip for supported types and arguments.
* **No effect from potentials**: Verify that the config is connected to the RF Diffusion node and parameters are set correctly.

### Need Help?
* Refer to the node tooltip for supported potential types and argument details.
* See [SaltAI documentation](https://docs.salt.ai/) for advanced configuration examples.
