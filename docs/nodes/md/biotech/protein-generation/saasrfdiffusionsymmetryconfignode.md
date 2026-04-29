# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node creates a symmetry configuration object for RF Diffusion-based protein generation. It turns user-facing symmetry and geometric options into a structured configuration while validating that the symmetry specification is supported. The resulting config controls how symmetric subunits are arranged in space and whether symmetry is enforced during the diffusion process.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionsymmetryconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you want RF Diffusion to design symmetric protein assemblies (e.g., cyclic oligomers, dihedral complexes, or polyhedral cages). Place it upstream of the RF Diffusion node and connect its `symmetry_config` output to the RF Diffusion node’s `symmetry_config` input. Typical scenarios: (1) De novo symmetric oligomer design: choose a symmetry such as `c3` and a suitable `radius`, then run RF Diffusion with unconditional `contigs`; (2) Symmetric design conditioned on an input PDB: combine this node with `RF Diffusion Contigmap Config` and optionally `RF Diffusion Scaffold Guided Config` to simultaneously control symmetry and sequence/structure constraints. It is commonly used together with the suite of RF Diffusion helper nodes: `RF Diffusion Contigmap Config`, `RF Diffusion Denoiser Config`, `RF Diffusion Potentials Config`, and `RF Diffusion Scaffold Guided Config`. Prefer using this node instead of manually building symmetry dictionaries, as it enforces allowed formats and consistent field names.

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
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Recenter the designed symmetric complex so its geometric center lies at the origin of the coordinate system. This is useful for normalized coordinates and more consistent visualization or docking. True enables recentering; false leaves the complex in its native frame.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Distance from the symmetry center (in Å) where symmetric subunits are placed. Must be at least 1. Larger values spread subunits further from the axis or center, yielding larger rings or shells; very small values can cause steric clashes between subunits.</td><td style="word-wrap: break-word;">25</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, only neighboring subunits of the asymmetric unit are explicitly modeled rather than the full symmetric assembly. This can reduce computational cost when you only care about local interactions around one unit; set to false to consider the entire assembly.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">symmetric_self_cond</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, symmetry is enforced via symmetric self-conditioning during intermediate diffusion steps, encouraging consistent symmetry throughout sampling. Set to false to relax intermediate symmetry enforcement, which may increase diversity at the cost of symmetry quality.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">symmetry</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of symmetry to apply. Valid entries are: empty string for no explicit symmetry; cyclic symmetry as 'cn' (e.g. 'c2', 'c3', 'c6'); dihedral symmetry as 'dn' (e.g. 'd2', 'd5'); or one of 'tetrahedral', 'octahedral', 'icosahedral' for polyhedral symmetries. Any other value causes a validation error.</td><td style="word-wrap: break-word;">c3</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">A dictionary-like symmetry configuration containing the validated symmetry type and flags (recenter, radius, model_only_neighbors, symmetric_self_cond). This object is intended to be connected directly to the RF Diffusion node’s `symmetry_config` input, where it governs symmetric layout and conditioning during protein design.</td><td style="word-wrap: break-word;">{'recenter': True, 'radius': 25, 'model_only_neighbors': False, 'symmetric_self_cond': True, 'symmetry': 'c3'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Strict symmetry validation**: The node checks that `symmetry` is either empty, matches cn/dn (e.g. 'c2', 'd5'), or is exactly 'tetrahedral', 'octahedral', or 'icosahedral'. Any other string raises a clear ValueError before RF Diffusion is invoked.
- **Empty symmetry behavior**: If `symmetry` is left empty or whitespace, it is converted to a null/None value internally, effectively disabling explicit symmetry while preserving other options such as recentering and neighbor modeling.
- **Radius tuning**: Very small `radius` values can produce heavily clashing assemblies, while extremely large values may yield unrealistic, over-extended complexes. For many ring-like oligomers and cages, moderate radii (e.g. 10–40 Å) are a good starting range.
- **Neighbor-only trade-offs**: Enabling `model_only_neighbors` can significantly reduce compute and memory usage, but the model no longer sees the full assembly, which may matter for designs where long-range inter-subunit contacts are functionally important.

## Troubleshooting
- **Invalid symmetry format error**: If you see an error like `Expected `symmetry` to be '', cn/dn (e.g. c2, d5), or one of tetrahedral/octahedral/icosahedral but got ...`, correct the `symmetry` input to a supported value such as 'c3', 'd2', 'tetrahedral', or leave it empty.
- **Output appears non-symmetric**: Confirm that `symmetry` is set to a valid value and that this node’s `symmetry_config` output is connected to the RF Diffusion node’s `symmetry_config` input. Also check for stray spaces (e.g. 'c3 '), then re-run the workflow.
- **Clashing subunits**: If the designed complex has strong steric clashes between repeats, gradually increase `radius` and re-run. Consider disabling `model_only_neighbors` so the model can account for long-range packing of the entire assembly.
- **Workflow stops before diffusion**: Validation issues in this node prevent RF Diffusion from running. Inspect the error message; it typically indicates an unsupported `symmetry` string or, more rarely, a non-integer or out-of-range `radius`.
