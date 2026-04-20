# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs the contigmap configuration used by RF Diffusion to control which parts of a protein sequence or structure are masked, inpainted, or length-constrained. It parses user-friendly string specifications into validated RFdiffusion-compatible contig structures. The resulting configuration is consumed by the RF Diffusion node to drive conditional and inpainting-based protein design.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need fine-grained control over which residues RFdiffusion should modify or preserve in both sequence and structure space. Typical use cases include loop remodeling on a known scaffold, inpainting specific secondary structure segments, and constraining the total length of designed proteins. In a common workflow, you (1) set up your main generation run using `SaaSRFDiffusionNode` (RF Diffusion), (2) configure which parts to inpaint or fix using `SaaSRFDiffusionContigmapConfigNode`, then (3) connect its `contigmap_config` output to the `contigmap_config` input on `SaaSRFDiffusionNode`. It is often used alongside other RFdiffusion config nodes (`SaaSRFDiffusionSymmetryConfigNode`, `SaaSRFDiffusionDenoiserConfigNode`, `SaaSRFDiffusionPotentialsConfigNode`, `SaaSRFDiffusionScaffoldGuidedConfigNode`) to fully specify the design task. Keep mask specifications consistent with your PDB and `contigs` layout to avoid validation or runtime issues.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines sequence positions whose amino-acid identities should be inpainted (masked sequence) while optionally keeping structural context. Uses RFdiffusion-style contig notation (chain letter plus 1-based residue indices and ranges, separated by commas). Empty string means no sequence inpainting is requested for any region.</td><td style="word-wrap: break-word;">A35-50,B10-20</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines residue ranges whose 3D backbone structure should be masked and resampled. Typically used to erase parts of a scaffold and let RFdiffusion redesign them. Use chain-plus-range notation (e.g. B165-178); multiple segments can be comma-separated. Empty string disables structural inpainting.</td><td style="word-wrap: break-word;">A120-145,B10-25</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue ranges corresponding to helical secondary structure that should be specifically masked for inpainting. This allows targeted redesign of helices while leaving other elements less constrained. Format is comma-separated inclusive ranges; leave empty to avoid helix-specific masking.</td><td style="word-wrap: break-word;">A30-40,A80-88</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue ranges corresponding to beta-strand segments to be masked and inpainted. Use comma-separated inclusive ranges in the same style as other contig fields. Empty string means no strand-specific masking is applied.</td><td style="word-wrap: break-word;">B12-18,B45-52</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Loop regions to mask and inpaint, allowing RFdiffusion to redesign flexible connecting segments while preserving overall fold. Specify as comma-separated inclusive ranges; an empty string disables loop-specific masking.</td><td style="word-wrap: break-word;">A55-60,A98-103</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue ranges where you provide or preserve sequence explicitly while other positions may be inpainted. This is useful in partial sequence design where certain segments must remain fixed. Uses numeric inclusive ranges separated by commas; no chain letters. Empty string means no fixed sequence segments are specified here.</td><td style="word-wrap: break-word;">172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Total designed sequence length or allowed length range. Accepts either a single integer (e.g. 100) or a range like 100-150. This constrains how long the designed protein can be and is treated differently from the positional masking fields. Empty string means no explicit length constraint is enforced via this setting.</td><td style="word-wrap: break-word;">120-160</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">RFDIFFUSION_CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">A structured contigmap configuration dictionary derived from the input strings. Each field is parsed and validated into RFdiffusion-compatible internal representations (e.g. lists of segments or null values). Connect this output directly to the `contigmap_config` input of `SaaSRFDiffusionNode` to control inpainting and length constraints during RFdiffusion runs.</td><td style="word-wrap: break-word;">{'inpaint_seq': [['A', 35, 50], ['B', 10, 20]], 'inpaint_str': [['A', 120, 145], ['B', 10, 25]], 'inpaint_str_helix': [['A', 30, 40], ['A', 80, 88]], 'inpaint_str_strand': [], 'inpaint_str_loop': [['A', 55, 60], ['A', 98, 103]], 'provide_seq': [[172, 177], [200, 205]], 'length': [[120, 160]]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Configuration parsing and validation are lightweight relative to the RFdiffusion GPU run, so iterating on masks and length ranges has negligible impact on overall runtime.
- **Limitations**: All inputs must follow RFdiffusion-style contig syntax (e.g. A10-25 or 10-20,30-40); malformed or inconsistent ranges will trigger validation errors rather than being silently corrected.
- **Behavior**: The `length` field is treated as a sequence-length constraint, not as positional masks; leaving it empty hands more control over total length to default RFdiffusion behavior.
- **Behavior**: Empty strings for any of the inpainting and provide-sequence fields are interpreted as "no constraint" for that dimension, producing a valid but less restrictive configuration.
- **Integration**: This node does not inspect your PDB; you are responsible for ensuring that chain IDs and residue indices in your masks align with the PDB and the `contigs` layout used in `SaaSRFDiffusionNode`.

## Troubleshooting
- **Invalid contig syntax error**: If a ValueError mentions contig parsing or invalid ranges, re-check your input strings for format issues (wrong separators, non-numeric indices, reversed start/end, or invalid chain letters).
- **Unexpected design regions changed**: When RFdiffusion modifies residues you expected to be fixed, verify that those positions are not included in any inpainting fields and that `provide_seq` correctly covers the intended ranges.
- **Length not respected**: If generated proteins are much longer or shorter than expected, confirm your `length` input is either a single integer or a simple range without chain IDs or extra punctuation, and that it matches your intended design scenario.
- **Silent no-op masks**: If changing inpainting fields appears to have no effect, ensure that your ranges actually exist within the residue indices defined by your `contigs` parameter on `SaaSRFDiffusionNode` and the input PDB; out-of-range masks are effectively ignored downstream.
