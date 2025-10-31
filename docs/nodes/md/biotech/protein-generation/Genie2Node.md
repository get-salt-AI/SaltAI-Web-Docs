# Genie 2

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein backbone structures using the Genie 2 model. Supports two modes: unconditional generation across a range of sequence lengths, and motif scaffolding conditioned on an input PDB and contig layout. Returns the generated structure and, for scaffolding, the extracted motif structure.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/Genie2Node.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to design proteins either from scratch (unconditional mode) or by scaffolding specified motifs within a provided structure (conditional mode). For unconditional generation, leave input_pdb empty and optionally set a range of lengths with min_length, max_length, and length_step. For motif scaffolding, provide input_pdb and a contigs string defining fixed motif segments and flexible regions; length_step is ignored in this mode. Choose the checkpoint (epoch 40 for unconditional, epoch 30 for scaffolding) and adjust scale and batch_size as needed.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Model checkpoint to use. 40-epoch is recommended for unconditional generation; 30-epoch for motif scaffolding.</td><td style="word-wrap: break-word;">base/epoch.40</td></tr>
<tr><td style="word-wrap: break-word;">scale</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Sampling noise scale for the generator.</td><td style="word-wrap: break-word;">0.6</td></tr>
<tr><td style="word-wrap: break-word;">batch_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of samples processed per batch. Reduce if you run out of GPU memory.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">min_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Minimum sequence length to consider. Used for both modes; must be <= max_length.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum sequence length to consider. Used for both modes; must be >= min_length.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">length_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Step size between sampled sequence lengths in unconditional mode. Ignored in motif scaffolding mode.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines scaffold layout for motif scaffolding with fixed motif segments and flexible regions. Use residue ranges like A367-372 and flexible gaps like 10-10. Multiple motifs can be grouped with labels in parentheses (A, B, etc.). Chain breaks are not supported; all segments are sequentially connected.</td><td style="word-wrap: break-word;">A367-372(B)/10-10/A342-348(A)/10-20/B342-348(A)/10-10/B367-372(B)</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base seed for deterministic sampling.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Input structure for motif scaffolding. If provided, node performs conditional generation using contigs. Leave empty for unconditional generation.</td><td style="word-wrap: break-word;">PDB data or reference</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Generated protein structure. In unconditional mode, a design for each sampled length; in scaffolding mode, the scaffolded design.</td><td style="word-wrap: break-word;">Generated PDB object</td></tr>
<tr><td style="word-wrap: break-word;">motif.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Motif structure extracted from the generation (primarily relevant in scaffolding mode).</td><td style="word-wrap: break-word;">PDB object of the designed motif</td></tr>
</tbody>
</table>
</div>

## Important Notes
- For unconditional generation, do not provide input_pdb; contigs will be ignored.
- For motif scaffolding, both input_pdb and a valid contigs string are required; length_step is not used.
- Recommended checkpoints: base/epoch.40 for unconditional; base/epoch.30 for scaffolding.
- min_length must be less than or equal to max_length. For unconditional mode, lengths are sampled from max_length down to min_length in steps of length_step.
- Contigs format: use fixed motif ranges (e.g., A10-25), flexible gaps (e.g., 5-15), and optional motif groups in parentheses to distinguish segments (e.g., A367-372(B)). Chain breaks are not supported.
- Batch size impacts GPU memory; lower it if you encounter out-of-memory issues.
- Generated outputs include metadata such as the seed and configuration for traceability.

## Troubleshooting
- Error: Found 0 lengths between min_length and max_length with step length_step. Fix by ensuring max_length >= min_length and length_step > 0 and small enough to produce at least one length.
- Error: Expected min_length <= max_length. Set min_length to a value less than or equal to max_length.
- Error: For motif scaffolding task, contigs must be passed. Provide a valid contigs string when input_pdb is provided.
- Unexpected: Contigs used in unconditional mode. Note: contigs are ignored when input_pdb is not provided; remove or leave empty to avoid confusion.
- Results vary run-to-run: Set a fixed seed and keep other parameters constant to get deterministic outputs.
- Out of memory: Reduce batch_size or the target lengths; try a smaller max_length or increase length_step so fewer lengths are sampled in unconditional mode.
