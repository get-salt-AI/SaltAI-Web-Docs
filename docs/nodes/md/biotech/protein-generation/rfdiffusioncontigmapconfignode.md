# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a contig map configuration for RF Diffusion protein design runs. It parses user-friendly string ranges into a structured configuration, handling sequence/structure inpainting, provided (unmasked) sequence regions, and overall length constraints. Empty inputs become nulls, and non-length fields are converted to lists of segments.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/rfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to prepare the contig mapping parameters for an RF Diffusion generation workflow. Connect its output to the RF Diffusion node's contigmap_config input to control which residues are masked/unmasked in sequence or structure and to optionally constrain target length.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue segments whose sequence should be masked (i.e., to be inpainted). Accepts comma-separated contigs syntax; typically chain-lettered ranges or positions.</td><td style="word-wrap: break-word;">A100-120,B50-60</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue indices whose 3D structure should be masked (structure inpainting). Comma-separated ranges. Often chain-prefixed.</td><td style="word-wrap: break-word;">B165-178</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to unmask/provide explicitly (kept fixed). Comma-separated inclusive ranges.</td><td style="word-wrap: break-word;">172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target sequence length or a length range. When set, used as a string constraint rather than a list.</td><td style="word-wrap: break-word;">100-150</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Configuration dictionary for RF Diffusion contig mapping with keys: inpaint_seq (list or null), inpaint_str (list or null), provide_seq (list or null), length (string or null).</td><td style="word-wrap: break-word;">{'inpaint_seq': ['A100-120', 'B50-60'], 'inpaint_str': ['B165-178'], 'provide_seq': ['172-177', '200-205'], 'length': '100-150'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Inputs are parsed as comma-separated lists; surrounding brackets or quotes are stripped automatically.
- Empty strings become null in the output; non-length fields become lists of segments, while length remains a string.
- Ranges are inclusive. Use optional chain identifiers where appropriate (e.g., A100-120).
- This node only prepares configuration; it does not validate biological plausibility or enforce chain existence.

## Troubleshooting
- Unexpected nulls in output: Ensure the input fields are not left empty or filled with only spaces.
- Downstream node rejects contig segments: Verify your segment format (e.g., use commas to separate, ranges like 100-120, and chain labels where required).
- Length not respected downstream: Confirm that the RF Diffusion node is in a mode that uses length constraints and that length is provided as a single value or 'min-max' string.
- Inconsistent masking behavior: Double-check that sequence masking (inpaint_seq) and structure masking (inpaint_str) target the intended ranges and chains.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/protein-generation/rfdiffusioncontigmapconfignode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

