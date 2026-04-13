# RF Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs RFdiffusion to generate or redesign protein structures. Supports unconditional backbone generation by length, conditional design using an input PDB plus contigs, and advanced scaffold-guided or fold-conditioned design through optional configuration objects. Can optionally return intermediate trajectory structures and supports deterministic sampling via a base random seed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to create or redesign protein backbones with RFdiffusion. For basic use, provide a contig specification (and optionally an input PDB) to define sequence length and which regions are flexible vs fixed. For advanced control, connect configuration objects from the RF Diffusion Scaffold Guided, Symmetry, Contigmap, Denoiser, and Potentials Config nodes. Choose PROD for full runs, TEST for faster dry-runs, or MOCK to return predefined example results for pipeline testing.

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
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode for the RF Diffusion service. Common values are PROD (normal full run), TEST (lightweight or reduced settings for quick checks), and MOCK (return static/demo outputs without running the underlying model).</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Contig specification describing sequence lengths and layout, and when used with an input PDB, which regions are fixed or flexible. Drives both unconditional (length-based) and conditional (structure-based) design behavior.</td><td style="word-wrap: break-word;">A1-80 10-20 B1-40</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB_FILE</td><td style="word-wrap: break-word;">Optional input protein structure used for conditional design or redesign. When provided, the contigs are interpreted relative to this structure to determine preserved vs redesigned segments.</td><td style="word-wrap: break-word;">starting_structure.pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_designs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of independent designs (samples) to generate in a single run. Higher values increase compute cost but provide more diversity.</td><td style="word-wrap: break-word;">8</td></tr>
<tr><td style="word-wrap: break-word;">base_seed</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for the diffusion sampling process. Using the same seed and parameters helps make runs approximately reproducible.</td><td style="word-wrap: break-word;">12345</td></tr>
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td>False</td><td style="word-wrap: break-word;">SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">Optional scaffold-guided (fold-conditioned) configuration object, typically produced by the RF Diffusion Scaffold Guided Config node. Encodes scaffold topologies, loop masks, and optional binder/target conditioning.</td><td style="word-wrap: break-word;"><SCAFFOLDGUIDED_CONFIG_OBJECT></td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">Optional symmetry configuration, usually created by the RF Diffusion Symmetry Config node, to enforce cyclic, dihedral, or polyhedral symmetry during backbone generation.</td><td style="word-wrap: break-word;"><SYMMETRY_CONFIG_OBJECT></td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">Optional contig map configuration that refines how contig strings map to chains and structural segments, enabling more explicit control over chain-level layout.</td><td style="word-wrap: break-word;"><CONTIGMAP_CONFIG_OBJECT></td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">DENOISER_CONFIG</td><td style="word-wrap: break-word;">Optional denoiser schedule configuration from the RF Diffusion Denoiser Config node, controlling the translation (C-alpha) and rotation noise levels across diffusion steps.</td><td style="word-wrap: break-word;"><DENOISER_CONFIG_OBJECT></td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">Optional guiding potentials configuration, generally created by the RF Diffusion Potentials Config node. Specifies structural or energetic bias terms (e.g., contact potentials, compactness, ligand interactions) used to guide generation.</td><td style="word-wrap: break-word;"><POTENTIALS_CONFIG_OBJECT></td></tr>
<tr><td style="word-wrap: break-word;">return_trajectory</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether to return intermediate diffusion trajectory structures in addition to final designs. Enable when you need to inspect the generative pathway.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">timeout_seconds</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum allowed wall-clock time in seconds for the RF Diffusion job before it is aborted.</td><td style="word-wrap: break-word;">3600</td></tr>
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
<tr><td style="word-wrap: break-word;">designed_pdbs</td><td style="word-wrap: break-word;">PDB_FILES</td><td style="word-wrap: break-word;">Collection of final designed protein backbones as PDB files, typically one for each requested design.</td><td style="word-wrap: break-word;">['design_0001.pdb', 'design_0002.pdb']</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Run metadata, including details such as execution mode, seeds, parsed contigs, configuration flags, and any scores or diagnostic information emitted by the backend.</td><td style="word-wrap: break-word;">{'mode': 'PROD', 'num_designs': 8, 'base_seed': 12345}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pdbs</td><td style="word-wrap: break-word;">PDB_FILES</td><td style="word-wrap: break-word;">Optional list of PDB structures representing intermediate diffusion trajectory steps for each design. Present only when return_trajectory is true.</td><td style="word-wrap: break-word;">['design_0001_step_001.pdb', 'design_0001_step_050.pdb']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Contig specification is critical**: The contigs input largely determines how RFdiffusion interprets the design task. Incorrect or incompatible contigs (especially when using an input PDB) are a common source of failed or poor designs.
- **Configuration objects are modular and optional**: Symmetry, scaffold-guided, contigmap, denoiser, and potentials configs can be connected independently. Start simple and add them stepwise to avoid over-constraining runs.
- **Execution mode affects behavior**: Use PROD for real design work, TEST for rapid functional checks at lower cost, and MOCK only when you explicitly want canned/demo outputs for debugging pipelines.
- **Determinism is best-effort**: The base_seed improves reproducibility but does not absolutely guarantee identical results across service or hardware changes.
- **Trajectory output is heavy**: Enabling return_trajectory can significantly increase runtime, memory, and storage usage. Reserve it for debugging or method development rather than routine batch runs.

## Troubleshooting
- **Issue: No designs or empty PDBs produced**: Confirm that contigs are valid and correctly formatted, especially relative to any input_pdb. Ensure the node is not in MOCK mode if you expect real generations.
- **Issue: Designs are overly similar or lack diversity**: Vary the base_seed between runs, increase num_designs, and review any strong potentials or symmetry/scaffold constraints that might be over-restricting the search.
- **Issue: Jobs frequently hit the timeout**: Increase timeout_seconds, reduce num_designs, and consider disabling trajectory output or simplifying configuration objects (e.g., fewer or weaker potentials).
- **Issue: Symmetry or scaffold constraints do not appear in outputs**: Check that the appropriate *_config inputs are connected and valid, and verify that their internal parameters (e.g., symmetry type or scaffold list) are set as intended.
- **Issue: Outputs do not change after editing parameters**: Make sure the node is not running in MOCK mode, and that the workflow is being re-executed from this node after configuration changes.
