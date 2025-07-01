# Chroma

Runs inference of a Chroma model for protein structure generation, supporting both conditional and unconditional modes with extensive control over SDE integration parameters.

<img src="/images/nodes/biotech/protein-generation/chroma.png" alt="Chroma" class="rounded-lg">

## Quick Start

1. Set the desired number of integration steps and chain length.
2. (Optional) Provide an initial PDB for conditional generation.
3. Adjust SDE and Langevin parameters as needed.
4. Run the node to generate protein structures.

## Setup Guide

### 1. Basic Configuration
1. Choose the number of integration steps (`steps`) and chain length (`chain_length`).
2. Set SDE parameters such as `tspan`, `langevin_factor`, and `inverse_temperature`.
3. (Optional) Provide an initial PDB for conditional generation.

### 2. Advanced Options
1. Select integration and SDE functions (`integrate_func`, `sde_func`).
2. Configure output options like `trajectory_length` and `full_output`.
3. Use chain manipulation options if needed (`swap_chain_names`, `order_chain_names`).

## Basic Usage

### Unconditional Generation
* Leave `input_pdb` empty to generate new protein structures from scratch.
* Set `chain_length` and `steps` to control output size and detail.

### Conditional Generation
* Provide an `input_pdb` to guide the generation process.
* Use chain manipulation options to adjust output chain order or names.

## Configuration

### Required Inputs
| Field              | Description                                                        | Type     | Example           |
|--------------------|--------------------------------------------------------------------|----------|-------------------|
| steps              | Number of integration steps for the SDE.                           | INT      | 500               |
| chain_length       | Length of the protein chain.                                       | INT      | 100               |
| tspan              | Time span for SDE integration (comma-separated floats).             | STRING   | 1.0,0.001         |
| langevin_factor    | Strength of Langevin noise.                                        | FLOAT    | 2.0               |
| langevin_isothermal| Use isothermal Langevin SDE.                                       | BOOLEAN  | False             |
| inverse_temperature| Inverse temperature parameter for SDE.                             | FLOAT    | 10.0              |
| initialize_noise   | Initialize noise for SDE integration.                              | BOOLEAN  | True              |
| integrate_func     | Integration function to use.                                       | COMBO    | euler_maruyama    |
| sde_func           | SDE function to use.                                               | COMBO    | reverse_sde       |
| trajectory_length  | Number of steps in trajectory output (max: steps).                 | INT      | 200               |
| full_output        | Return full SDE integration outputs.                               | BOOLEAN  | False             |
| swap_chain_names   | Comma-separated chain names to swap after generation.               | STRING   | A,B               |
| order_chain_names  | Comma-separated chain names to specify output order.                | STRING   | A,B               |
| seed               | Base seed for randomness.                                          | INT      | 42                |
| mode               | Execution mode: MOCK, PROD, or TEST.                               | COMBO    | PROD              |

### Optional Inputs
| Field     | Description                                 | Type | Example |
|-----------|---------------------------------------------|------|---------|
| input_pdb | Initial PDB to start generation from.       | PDB  | {"1abc": "..."} |

### Outputs
| Field                | Description                                              | Type | Example |
|----------------------|----------------------------------------------------------|------|---------|
| generation.pdb       | Generation result.                                       | PDB  | {"gen_0": "..."} |
| trajectory.pdb       | Trajectory PDB (if `full_output` is True).               | PDB  | {"traj_0": "..."} |
| Xhat_trajectory.pdb  | Xhat trajectory PDB (if `full_output` is True).          | PDB  | {"xhat_0": "..."} |
| Xunc_trajectory.pdb  | Xunc trajectory PDB (if `full_output` is True).          | PDB  | {"xunc_0": "..."} |

## Best Practices

### Parameter Selection
* Start with default parameters for most use cases.
* Increase `steps` and `trajectory_length` for higher resolution outputs.

### Reproducibility & Modes
* Use the same `seed` for reproducible results.
* Use `MOCK` mode for workflow validation, `TEST` for quick checks, and `PROD` for full runs.

## Troubleshooting

### Common Issues
* **Output is empty or missing:** Ensure all required parameters are set and input PDB (if used) is valid.
* **Long runtime:** Reduce `steps` or use `TEST` mode for faster execution.
* **Chain manipulation not working:** Check that chain names are valid and comma-separated.

### Need Help?
* Contact support for further assistance.
