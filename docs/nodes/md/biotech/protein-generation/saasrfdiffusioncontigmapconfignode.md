# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a contigmap configuration JSON for RF Diffusion runs. It parses and validates string ranges describing which residues or secondary-structure regions to mask/unmask, and optionally the overall sequence length or length range.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define advanced sequence/structure masking for RF Diffusion. Connect its output to the contigmap_config input of the RF Diffusion node. For unconditional generation, only set length and leave other fields empty. For conditional or inpainting workflows, specify inpaint_* and/or provide_seq ranges that align with your input structure.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue ranges whose sequence should be masked for inpainting. Use the same contig format (chains and ranges).</td><td style="word-wrap: break-word;">A10-25,B5-12</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue indices to mask in 3D structure space. Include chain IDs when applicable.</td><td style="word-wrap: break-word;">B165-178</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue ranges to mask specifically for helix secondary structure.</td><td style="word-wrap: break-word;">10-20,30-40</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue ranges to mask specifically for beta strand secondary structure.</td><td style="word-wrap: break-word;">50-60,70-80</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue ranges to mask specifically for loop secondary structure.</td><td style="word-wrap: break-word;">25-29,65-69</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue ranges to unmask (i.e., keep fixed/provided sequence) during design. Use inclusive ranges; multiple ranges can be comma-separated.</td><td style="word-wrap: break-word;">172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Desired total sequence length or length range for unconditional generation.</td><td style="word-wrap: break-word;">100 or 100-150</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Validated contigmap configuration object to pass into the RF Diffusion node.</td><td style="word-wrap: break-word;">{"inpaint_seq": [["A",10,25],["B",5,12]], "inpaint_str": [["B",165,178]], "inpaint_str_helix": [[10,20],[30,40]], "inpaint_str_strand": [[50,60],[70,80]], "inpaint_str_loop": [[25,29],[65,69]], "provide_seq": [[172,177],[200,205]], "length": [100,150]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unconditional runs**: Only set length; all other inpainting fields should be empty. The RF Diffusion node enforces this and will error if other fields are provided.
- **Conditional/inpainting runs**: Provide proper chain-labeled ranges (e.g., A10-25) that align with your input structure.
- **Format validation**: All fields except length are validated as contig/range lists; length is validated as a single length or a numeric range.
- **Index consistency**: Ranges must correspond to actual residues in your input PDB when used with conditional or partial diffusion workflows.
- **Secondary structure masks**: The helix/strand/loop masking fields target specific secondary structure classes and are optional.

## Troubleshooting
- **Invalid range format**: Ensure ranges use inclusive numeric spans with optional chain IDs (e.g., A10-25, 50-60). Separate multiple ranges with commas.
- **Error during unconditional generation**: If you see errors about unexpected fields, clear inpaint_* and provide_seq, and only specify length.
- **Misaligned indices**: If diffusion fails or results look incorrect, verify that the provided ranges match residues and chain IDs in the input PDB.
- **Empty configuration not applied**: Leave fields blank (empty string) rather than placeholders; the node interprets empty as None.
- **Unexpected behavior with secondary structure masks**: Confirm the ranges target the intended regions and avoid overlaps that contradict provide_seq.
