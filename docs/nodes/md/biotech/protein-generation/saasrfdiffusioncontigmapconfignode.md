# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a contigmap configuration for RF Diffusion runs. It collects sequence/structure masking and unmasking directives (including secondary-structure-specific masks) and the target length/range, validates their formats, and outputs a single configuration object. Designed to be passed directly into the RF Diffusion node as its contigmap_config parameter.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to specify which sequence or structure regions to inpaint (mask) or provide (unmask) for an RF Diffusion design task, and/or to set the overall sequence length or length range. Connect the node’s output to the contigmap_config input of the RF Diffusion node as part of a protein design workflow.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Amino acid sequence regions to mask, using the same format as contigs (e.g., chain and index ranges). Leave empty if not masking by sequence.</td><td style="word-wrap: break-word;">A10-25,B40-55</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">3D structure indices to mask. Supports chain-prefixed ranges.</td><td style="word-wrap: break-word;">B165-178</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure-specific mask for helices. Comma-separated inclusive index ranges.</td><td style="word-wrap: break-word;">10-20,30-40</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure-specific mask for beta strands. Comma-separated inclusive index ranges.</td><td style="word-wrap: break-word;">50-60,70-80</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure-specific mask for loops. Comma-separated inclusive index ranges.</td><td style="word-wrap: break-word;">25-29,65-69</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to explicitly unmask (provide). Use comma-separated inclusive ranges.</td><td style="word-wrap: break-word;">172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Total sequence length or a length range. Provide a single integer or an inclusive range.</td><td style="word-wrap: break-word;">100-150</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Validated contigmap configuration to feed into the RF Diffusion node.</td><td style="word-wrap: break-word;">{"inpaint_seq": "A10-25", "inpaint_str": "B165-178", "inpaint_str_helix": "10-20,30-40", "inpaint_str_strand": "50-60", "inpaint_str_loop": "25-29,65-69", "provide_seq": "172-177,200-205", "length": "100-150"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input formats**: Use comma-separated inclusive ranges with hyphens (e.g., 10-20,30-40). Structure ranges may include an uppercase chain identifier prefix (e.g., B165-178).
- **Length field**: Must be a number or a numeric range only; do not include chain letters or residue codes in the length input.
- **Optional behavior via empty strings**: Leave any field empty if you do not wish to specify that particular mask/provide directive.
- **Downstream use**: Connect this output to the contigmap_config input of the RF Diffusion node.

## Troubleshooting
- **Validation error on ranges**: Ensure each range uses the format start-end, with start and end as integers, and that multiple ranges are comma-separated.
- **Chain prefix errors**: For structure masks, include a valid uppercase chain letter prefix only when needed (e.g., B165-178), and avoid prefixes for pure length inputs.
- **Unexpected rejection of input**: Remove any whitespace anomalies and verify there are no stray characters or missing hyphens in ranges.
- **No effect on masking**: Confirm the appropriate field is used (sequence vs. structure vs. secondary structure) and that the RF Diffusion node is consuming this config output.
