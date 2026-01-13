# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds the contig/masking configuration used by RF Diffusion. It defines which sequence or structure regions are masked or preserved, plus optional total length constraints. All fields use the same range syntax as the main contigs input (chain-aware ranges like A10-25, flexible gaps like 5-15, comma-separated lists where applicable).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to prepare detailed masking instructions for sequence and structure when generating or conditioning designs. Connect its output to the contigmap_config input of the RF Diffusion node. Typical workflow: set contigs and other generation parameters in RF Diffusion, then add optional inpainting/masking and length limits here to fine-tune which residues are redesigned or preserved.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to mask (redesign). Uses the same format as contigs. Supports chain-aware fixed regions (e.g., A10-25), flexible gaps (e.g., 5-15), and multiple segments separated by commas.</td><td style="word-wrap: break-word;">A10-25,B50-60,5-15</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">3D structure regions to mask by explicit residue indices. Typically chain-aware ranges.</td><td style="word-wrap: break-word;">A30-45,B165-178</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Helical secondary-structure residues to mask. Comma-separated inclusive ranges.</td><td style="word-wrap: break-word;">10-20,30-40</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Beta-strand secondary-structure residues to mask. Comma-separated inclusive ranges.</td><td style="word-wrap: break-word;">50-60,70-80</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Loop secondary-structure residues to mask. Comma-separated inclusive ranges.</td><td style="word-wrap: break-word;">25-29,65-69</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to unmask (keep fixed). Comma-separated inclusive ranges.</td><td style="word-wrap: break-word;">A172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Total sequence length or a range. Used mainly for unconditional designs; can also constrain conditional runs. Leave empty to not constrain.</td><td style="word-wrap: break-word;">100 or 100-150</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">RFDIFFUSION_CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">A structured configuration object describing sequence and structure masking/unmasking and optional length constraints for RF Diffusion.</td><td style="word-wrap: break-word;">{"inpaint_seq": ["A10-25","B50-60"], "inpaint_str": ["A30-45"], "provide_seq": ["A172-177"], "length": "100-150"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Masks follow the same syntax as the main contigs input: use chain IDs for fixed regions (e.g., A10-25), use numeric ranges for flexible gaps (e.g., 5-15), and separate multiple items with commas.
- Only length should be set for unconditional generation; other masking fields are typically left empty. For conditional or inpainting scenarios, use the masking fields alongside a valid contigs layout.
- The helix/strand/loop fields specifically mask secondary-structure classes; use inpaint_str for general residue index masking.
- Ranges are inclusive. Ensure they match the residue numbering and chains of the input structure when used in conditional designs.
- Leave fields empty if not needed; empty strings are treated as no constraint/mask for that category.

## Troubleshooting
- Invalid range format: Use inclusive ranges like A10-25 or 50-60, separated by commas. Avoid spaces around hyphens.
- Chain mismatch: If using chain-aware ranges (e.g., A10-25), verify the chain IDs exist in the input structure.
- Overlapping or conflicting masks: If provide_seq (unmask) overlaps with inpaint_* (mask), refine regions to avoid ambiguity.
- Length conflicts: If 'length' contradicts the total residues implied by your contigs or input structure, adjust length or the contig layout.
- Empty results or ignored masks: Ensure you are running a conditional design with an input structure when using structure-based masks (inpaint_str and secondary-structure masks).
