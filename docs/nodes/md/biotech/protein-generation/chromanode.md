# Chroma

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs Chroma-based protein generation using stochastic differential equation (SDE) integration. Supports unconditional generation or conditional sampling from an optional input PDB, with fine-grained control over integration steps, SDE function, integrator, noise, and temperature. Can optionally return sampled trajectories (protein, Xhat, Xunc) in addition to the final generated structure.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/chromanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to generate protein structures with Chroma. For unconditional generation, provide chain_length and relevant SDE settings. For conditional generation or motif handling, supply an input PDB and optionally reorder or swap chain names after generation. Enable full_output to retrieve trajectory PDBs; otherwise only the final generation is returned. Modes allow fast testing (TEST) and offline mocking (MOCK) for development.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">steps</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of integration steps for the SDE.</td><td style="word-wrap: break-word;">500</td></tr>
<tr><td style="word-wrap: break-word;">chain_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Length of the protein chain to generate for unconditional runs.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">tspan</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Time span for SDE integration as a comma-separated sequence of floats. Parsed into a list of floats.</td><td style="word-wrap: break-word;">1.0, 0.001</td></tr>
<tr><td style="word-wrap: break-word;">langevin_factor</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Strength scaling for Langevin noise.</td><td style="word-wrap: break-word;">2.0</td></tr>
<tr><td style="word-wrap: break-word;">langevin_isothermal</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Use isothermal version of the Langevin SDE.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">inverse_temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Inverse temperature parameter for the SDE.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">initialize_noise</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to initialize noise before integration.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">integrate_func</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Integration function to use.</td><td style="word-wrap: break-word;">euler_maruyama</td></tr>
<tr><td style="word-wrap: break-word;">sde_func</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">SDE function type.</td><td style="word-wrap: break-word;">reverse_sde</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sampled steps to include in the trajectory outputs (capped by steps).</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">full_output</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, also returns trajectory, Xhat trajectory, and Xunc trajectory PDBs.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">swap_chain_names</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Two comma-separated chain names to swap after generation. Must contain exactly two names when provided.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">order_chain_names</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Two comma-separated chain names to explicitly order after generation. Must contain exactly two names when provided.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base seed for deterministic runs. Per-output seeds are derived from this base.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Run mode. MOCK uses predefined data, PROD calls the service, TEST reduces work for faster iteration.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional starting PDB for conditional generation. The first PDB detected is used.</td><td style="word-wrap: break-word;">PDB object reference</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Final generated protein structure.</td><td style="word-wrap: break-word;">PDB object with final model(s)</td></tr>
<tr><td style="word-wrap: break-word;">trajectory.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Sampled trajectory PDB. Returned only when full_output is true; otherwise may be empty.</td><td style="word-wrap: break-word;">PDB object containing trajectory frames</td></tr>
<tr><td style="word-wrap: break-word;">Xhat_trajectory.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Xhat trajectory PDB. Returned only when full_output is true; otherwise may be empty.</td><td style="word-wrap: break-word;">PDB object with Xhat trajectory frames</td></tr>
<tr><td style="word-wrap: break-word;">Xunc_trajectory.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Xunc trajectory PDB. Returned only when full_output is true; otherwise may be empty.</td><td style="word-wrap: break-word;">PDB object with Xunc trajectory frames</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Mode behavior: MOCK returns predefined mock results; TEST mode overrides steps to 100, trajectory_length to 100, and disables full_output; PROD runs the actual service.
- tspan must be a comma-separated list of floats; it is parsed before execution.
- swap_chain_names and order_chain_names require exactly two comma-separated chain IDs when used.
- Only one sample is produced per execution (samples is internally fixed to 1).
- If input_pdb is provided, the first PDB is extracted and used for conditional generation.
- Trajectory outputs are only meaningful when full_output is true; otherwise they may be empty placeholders.
- Timeout differs by case: longer when input_pdb is provided.

## Troubleshooting
- Invalid tspan format: Ensure it is a comma-separated list of floats, e.g., "1.0, 0.001".
- Chain name errors: swap_chain_names and order_chain_names must contain exactly two comma-separated chain IDs (e.g., A,B).
- Missing trajectories: Set full_output to true and ensure trajectory_length <= steps.
- Very long runs: In TEST mode, steps and trajectory_length are reduced to speed up iteration. For production-scale results, use PROD mode and adjust steps/trajectory_length accordingly.
- Conditional run issues: Provide a valid input_pdb for conditional generation; ensure the file contains a recognizable PDB entry.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/protein-generation/chromanode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

