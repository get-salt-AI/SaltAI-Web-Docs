# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds the contigmap configuration used to control masking and length constraints for RF Diffusion protein design. It lets you specify which sequence or structural regions to inpaint (mask), which residues to keep (provide), and optionally enforce overall sequence length. Secondary-structure-specific masking for helices, strands, and loops is supported.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to define masking rules and/or length constraints for an RF Diffusion generation. Connect its output to the contigmap_config input of the RF Diffusion node. In conditional designs, use inpaint_* and provide_seq to selectively redesign parts of the input; in unconditional designs, leave all fields empty except length.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence masking specification in the same syntax as contigs. Defines regions whose amino-acid sequence should be masked for redesign.</td><td style="word-wrap: break-word;">A10-25, B5-15</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">3D structure masking by chain and residue index range. Indicates regions where structure should be masked.</td><td style="word-wrap: break-word;">B165-178</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure-specific masking for helices; comma-separated inclusive index ranges.</td><td style="word-wrap: break-word;">10-20,30-40</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure-specific masking for beta strands; comma-separated inclusive index ranges.</td><td style="word-wrap: break-word;">50-60,70-80</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Secondary-structure-specific masking for loops; comma-separated inclusive index ranges.</td><td style="word-wrap: break-word;">25-29,65-69</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated inclusive ranges of sequence to unmask/keep fixed (i.e., preserve existing sequence for these regions).</td><td style="word-wrap: break-word;">172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Desired overall sequence length or an allowed range.</td><td style="word-wrap: break-word;">100 or 100-150</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">RFDIFFUSION_CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">Structured contigmap configuration to control masking and length constraints in RF Diffusion.</td><td style="word-wrap: break-word;">{"inpaint_seq": "A10-25", "inpaint_str": "B165-178", "inpaint_str_helix": "10-20", "inpaint_str_strand": "50-60", "inpaint_str_loop": "25-29", "provide_seq": "172-177", "length": "100-150"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unconditional design rule**: In unconditional generation, only length should be set; all other contigmap fields must be left empty, otherwise the RF Diffusion node will raise an error.
- **Syntax expectations**: Ranges are inclusive. Chain-specific notations like A10-25 are supported where applicable (e.g., inpaint_seq and inpaint_str). For secondary-structure masks (helix/strand/loop), use comma-separated index ranges without chain letters unless your workflow requires them.
- **Providing vs. inpainting**: Ranges in provide_seq are preserved (unmasked), while ranges in inpaint_* are masked and redesigned.
- **Optional usage**: This configuration is optional for the RF Diffusion node. Leave all fields empty if you do not need specific masking or length constraints.
- **Secondary-structure masks**: helix/strand/loop masking is supported for finer control of redesign regions.

## Troubleshooting
- **Invalid range format**: If you see a validation error, check that ranges are inclusive and correctly formatted (e.g., A10-25, 50-60) and comma-separated without extra spaces.
- **Unconditional mode errors**: If running unconditional design and you filled any inpaint_* or provide_seq fields, clear them and only set length.
- **No effect from provide_seq**: Ensure provide_seq ranges do not overlap with inpaint_* ranges; overlapping specifications can lead to unexpected behavior.
- **Unexpected chain errors**: For inputs that require chain-qualified ranges (e.g., A10-25), ensure the chain IDs and indices match your input structure.
- **Empty but required inputs**: All inputs are present in the UI, but you may leave them blank if not needed; blanks are treated as no constraint.
