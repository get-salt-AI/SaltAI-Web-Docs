# RF Diffusion Contigmap Config

Builds a structured contigmap configuration for RF Diffusion. It accepts masking and length specifications and returns a validated JSON dict ready to plug into the RF Diffusion node. All fields are strings that are parsed into structured ranges; invalid formats will be rejected.

## Usage

Use this node to define which sequence/structure regions should be masked or preserved, and to specify overall design length constraints. Connect its output to the 'contigmap_config' input of the RF Diffusion node. For unconditional generation, only 'length' should be set; for conditional or inpainting workflows, use the other fields to target specific regions.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Regions whose sequence should be masked for inpainting. Uses the same segment syntax as contigs (supports chain IDs, fixed ranges, gaps).</td><td style="word-wrap: break-word;">A10-25/5-10/A40-55</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue index ranges (optionally with chain IDs) whose 3D structure should be masked for inpainting.</td><td style="word-wrap: break-word;">B165-178</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Inclusive index ranges to mask helix secondary structure. Multiple ranges allowed, comma-separated.</td><td style="word-wrap: break-word;">10-20,30-40</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Inclusive index ranges to mask beta strand secondary structure. Multiple ranges allowed, comma-separated.</td><td style="word-wrap: break-word;">50-60,70-80</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Inclusive index ranges to mask loop secondary structure. Multiple ranges allowed, comma-separated.</td><td style="word-wrap: break-word;">25-29,65-69</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Inclusive sequence ranges to unmask (i.e., provide/fix) during design. Multiple ranges allowed, comma-separated.</td><td style="word-wrap: break-word;">172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target sequence length as a single integer or a range. Used for unconditional or general length constraints.</td><td style="word-wrap: break-word;">100-150</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A validated configuration dict for contigmap-related parameters to pass into RF Diffusion.</td><td style="word-wrap: break-word;">{'inpaint_seq': [['A10-25'], ['5-10'], ['A40-55']], 'inpaint_str': [['B165-178']], 'inpaint_str_helix': [['10-20'], ['30-40']], 'inpaint_str_strand': [['50-60'], ['70-80']], 'inpaint_str_loop': [['25-29'], ['65-69']], 'provide_seq': [['172-177'], ['200-205']], 'length': ['100-150']}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input formats matter**: All fields are parsed; use inclusive ranges like 10-20 and optional chain IDs like A10-25. Separate multiple ranges with commas; separate segments with '/' where applicable.
- **Unconditional generation constraint**: When using this with unconditional RF Diffusion, only 'length' should be set. Any non-empty values in other fields will cause the RF Diffusion node to error.
- **Secondary structure masks**: The helix/strand/loop masking fields are supported by newer RF Diffusion updates; ensure your backend supports them.
- **Empty fields**: Leaving a field empty means 'no constraint' for that parameter; it will be passed as null/absent in the resulting configuration.

## Troubleshooting
- **Invalid format error**: If you see a validation error, check for typos in ranges (use N-M), ensure chain IDs precede ranges (e.g., A10-25), and use commas for multiple ranges.
- **Unconditional mode rejections**: If RF Diffusion reports that certain keys must be empty in unconditional mode, clear 'inpaint_seq', 'inpaint_str*', and 'provide_seq', leaving only 'length'.
- **No effect from masks**: Verify your indices and chain IDs match the input structure used by RF Diffusion; mismatched numbering or chain labels will result in masks not applying.
