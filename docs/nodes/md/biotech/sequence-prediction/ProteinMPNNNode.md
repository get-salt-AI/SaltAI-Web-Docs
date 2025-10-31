# ProteinMPNN

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Designs protein sequences for given 3D backbone structures using ProteinMPNN. Supports CA-only parsing, soluble-only weights, temperature-controlled sampling, residue omission, chain-specific design, batching, and multiple model variants. Returns all generated sequences and the top-scoring subset as FASTA.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/sequence-prediction/ProteinMPNNNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to generate and rank amino acid sequences compatible with input protein structures. Provide one or more PDB structures, choose the ProteinMPNN model and options (e.g., sampling temperatures, omit residues, chains to design), then integrate the output FASTA into downstream evaluation or structure prediction workflows.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Protein backbone structures to design sequences against. Typically a collection mapping structure names to PDB-formatted text.</td><td style="word-wrap: break-word;">{'my_protein_A.pdb': 'ATOM ...', 'complex_AB.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">ca_only</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, parse CA-only structures and use CA-only models.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">v_48_002 \| v_48_010 \| v_48_020 \| v_48_030</td><td style="word-wrap: break-word;">ProteinMPNN model variant to use.</td><td style="word-wrap: break-word;">v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">use_soluble_model</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, use weights trained on soluble proteins only.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise added to backbone atoms to increase sequence diversity (0.0–1.0).</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many sequences to generate per input structure.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">num_best_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many top-scoring sequences to select per input after generation. Must be <= num_seq_per_target.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">batch_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Batch size for generation. Reduce if you encounter GPU memory limits.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum total sequence length allowed.</td><td style="word-wrap: break-word;">200000</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of temperatures controlling sampling diversity (e.g., 0.2,0.25,0.5). Higher values increase diversity.</td><td style="word-wrap: break-word;">0.1,0.15,0.2,0.25,0.3</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of one-letter amino acid codes to exclude from generation.</td><td style="word-wrap: break-word;">C,M,W</td></tr>
<tr><td style="word-wrap: break-word;">chains_to_design</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain identifiers to design. Leave empty to design all chains.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">MOCK \| PROD \| TEST</td><td style="word-wrap: break-word;">Execution mode. MOCK returns predefined sequences for quick demos, TEST minimizes generation for speed, PROD runs full generation.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">All generated sequences across temperatures and samples in FASTA format.</td><td style="word-wrap: break-word;">>my_protein_A_0 ACDEFGHIK... >my_protein_A_1 ...</td></tr>
<tr><td style="word-wrap: break-word;">best_seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Top-scoring subset of generated sequences per input structure in FASTA format.</td><td style="word-wrap: break-word;">>my_protein_A_best_0 ACDEFGHIK...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- CA-only and soluble-only configurations cannot be used together. Set either ca_only or use_soluble_model to false.
- The model v_48_030 is not available for CA-only. If ca_only is true, choose another model.
- num_best_seq_per_target must be less than or equal to num_seq_per_target.
- In TEST mode, num_seq_per_target is forced to 1 to speed up runs.
- sampling_temp, omit_amino_acids, and chains_to_design accept comma-separated inputs; they are internally normalized for the service.
- Timeout scales with the number of input structures; very large batches or long sequences can require more time.
- Batch size influences memory usage; lower it if encountering resource limits.

## Troubleshooting
- Error: CA-SolubleMPNN model is not available yet. Set either ca_only or use_soluble_model to False. Fix by disabling one of the two options.
- Error: CA-v_48_030 model is not available yet. Set ca_only to False or pick another model_name. Fix by selecting v_48_002, v_48_010, or v_48_020.
- Error: Expected num_best_seq_per_target <= num_seq_per_target. Fix by reducing num_best_seq_per_target or increasing num_seq_per_target.
- Generated sequences seem sparse or identical: increase sampling_temp values or backbone_noise to boost diversity.
- Out-of-memory or long runtimes: reduce batch_size, decrease num_seq_per_target, or limit max_length.
- Unexpected or invalid list inputs: ensure sampling_temp, omit_amino_acids, and chains_to_design are comma-separated without extra spaces; e.g., 0.1,0.2 or A,B.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/sequence-prediction/ProteinMPNNNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

