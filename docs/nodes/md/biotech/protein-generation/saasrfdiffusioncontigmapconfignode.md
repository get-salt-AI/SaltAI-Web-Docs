# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a Contigmap configuration JSON for RF Diffusion. It parses user-friendly string inputs that describe which sequence/structure regions to mask or keep, as well as the intended sequence length or range. The result is a structured config dictionary to plug into the RF Diffusion node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when preparing RF Diffusion runs that need explicit control over masked regions (sequence or structure), selective unmasking, and sequence length constraints. Connect its output to the RF Diffusion node's contigmap_config input. In unconditional runs, typically only the length is set and other fields are left empty.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to mask (same format style as contigs). Use chain/range syntax for conditional scenarios.</td><td style="word-wrap: break-word;">A10-25/5-10/A60-75</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">3D structure regions to mask (by chain and residue index range).</td><td style="word-wrap: break-word;">B165-178</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure helix regions to mask (comma-separated inclusive ranges).</td><td style="word-wrap: break-word;">10-20,30-40</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure beta-strand regions to mask (comma-separated inclusive ranges).</td><td style="word-wrap: break-word;">50-60,70-80</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure loop regions to mask (comma-separated inclusive ranges).</td><td style="word-wrap: break-word;">25-29,65-69</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to explicitly unmask (keep provided sequence). Useful to fix parts of the diffused regions.</td><td style="word-wrap: break-word;">172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Desired sequence length or a range. For unconditional designs, specify only length or a range.</td><td style="word-wrap: break-word;">100-150</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Contigmap configuration to pass into RF Diffusion.</td><td style="word-wrap: break-word;">{'inpaint_seq': 'A10-25/5-10/A60-75', 'inpaint_str': 'B165-178', 'inpaint_str_helix': '10-20,30-40', 'inpaint_str_strand': '50-60,70-80', 'inpaint_str_loop': '25-29,65-69', 'provide_seq': '172-177,200-205', 'length': '100-150'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Empty strings are allowed for all inputs; they will be treated as not set.
- In unconditional generation, only length should be provided; other fields should be left empty.
- Ranges are inclusive. Multiple ranges are typically comma-separated; complex sequence layouts may use chain/range syntax similar to contigs.
- Secondary-structure mask fields (helix/strand/loop) are supported by newer RF Diffusion updates; ensure your backend supports them.
- Ensure your region specifications match any input structure and overall contigs layout used in RF Diffusion.

## Troubleshooting
- Validation error: Check formatting of ranges and chain IDs (e.g., use A10-25 or comma-separated ranges like 10-20,30-40).
- Unconditional run rejected: Clear all mask fields and only provide length.
- Conditional run issues: Ensure regions align with your input PDB chains and residue numbering.
- Unexpected behavior with secondary-structure masks: Remove helix/strand/loop masks to test baseline, then reintroduce gradually.
- Downstream RF Diffusion errors: Verify contigmap_config matches the contigs strategy used in the RF Diffusion node.
