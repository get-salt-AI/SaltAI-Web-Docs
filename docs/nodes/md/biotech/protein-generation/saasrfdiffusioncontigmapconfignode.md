# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds the contig/masking configuration used by RF Diffusion. It defines which sequence and structure regions are masked (redesigned) or preserved and can apply optional total length constraints. All regions use the same flexible range syntax as the main contigs input, including chain-aware ranges and variable-length gaps.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need fine-grained control over which residues are redesigned versus kept fixed in RF Diffusion runs. Configure sequence and structure masks plus optional length constraints, then connect the output to the contigmap_config input of the RF Diffusion node. Typically you first set overall contigs and generation parameters in RF Diffusion, then add this node to specify inpainting/masking details and length limits for conditional or inpainting workflows.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to mask (redesign). Uses the same syntax as contigs: chain-aware fixed regions (e.g., A10-25), flexible gaps (e.g., 5-15), and multiple segments separated by commas. Residues in these ranges will be inpainted rather than preserved.</td><td style="word-wrap: break-word;">A10-25,B50-60,5-15</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">3D structure regions to mask by explicit residue indices, typically with chain-aware ranges. These define parts of the input structure whose coordinates will be treated as masked for redesign.</td><td style="word-wrap: break-word;">A30-45,B165-178</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Helical secondary-structure residues to mask. Comma-separated inclusive ranges that specifically target helices for inpainting, based on residue indices.</td><td style="word-wrap: break-word;">10-20,30-40</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Beta-strand secondary-structure residues to mask. Comma-separated inclusive ranges for selecting strand segments to redesign.</td><td style="word-wrap: break-word;">50-60,70-80</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Loop secondary-structure residues to mask. Comma-separated inclusive ranges, useful for redesigning loops while keeping helices and strands more constrained.</td><td style="word-wrap: break-word;">25-29,65-69</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to unmask (keep fixed). Comma-separated inclusive ranges that specify residues whose sequence should be preserved rather than redesigned.</td><td style="word-wrap: break-word;">A172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Total sequence length or a length range that constrains designs. Primarily used for unconditional generation but can also limit conditional runs. Leave empty if you do not want to constrain total length.</td><td style="word-wrap: break-word;">100 or 100-150</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">RFDIFFUSION_CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">Structured configuration object describing sequence and structure masking/unmasking behavior and optional length constraints for RF Diffusion. This encapsulates all inpaint and provide regions in a normalized format consumable by the RF Diffusion node.</td><td style="word-wrap: break-word;">{"inpaint_seq": ["A10-25","B50-60"], "inpaint_str": ["A30-45"], "provide_seq": ["A172-177"], "length": "100-150"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Mask syntax**: All masks follow the same syntax as the main contigs input: use chain IDs for fixed regions (e.g., A10-25), numeric ranges for flexible segments or gaps (e.g., 5-15), and commas to separate multiple items.
- **Unconditional vs conditional**: For unconditional generation, typically only length is set and other masking fields are left empty. For conditional or inpainting designs, combine this config with a valid contigs layout and an input structure.
- **Secondary-structure targeting**: The inpaint_str_helix, inpaint_str_strand, and inpaint_str_loop fields specifically target helices, strands, and loops. Use inpaint_str for more general residue index–based masking.
- **Inclusive ranges and numbering**: All ranges are inclusive. Ensure they are consistent with residue numbering and chain IDs in your input structure, especially for chain-aware ranges like A10-25.
- **Empty fields**: Leaving a field empty indicates no mask or constraint for that category; empty strings are safely treated as no-op for that mask type.

## Troubleshooting
- **Invalid range format**: If ranges are rejected or ignored, confirm they use inclusive formats like A10-25 or 50-60, with commas between items and no spaces around hyphens.
- **Chain ID mismatches**: If conditional runs behave unexpectedly, verify that all chain-aware ranges (e.g., A10-25) reference chain IDs that actually exist in the input structure.
- **Overlapping masks**: If redesign regions do not match expectations, check for overlaps between provide_seq (preserve) and inpaint_* (mask) ranges and revise them to avoid conflicts.
- **Length conflicts**: If runs fail or designs are truncated or extended incorrectly, ensure the length field is compatible with your contigs and input structure; adjust either the length or contig layout.
- **Masks ignored in practice**: If structure-based masks (e.g., inpaint_str or secondary-structure masks) appear ineffective, make sure you are running a conditional design with a valid input structure connected to RF Diffusion.
