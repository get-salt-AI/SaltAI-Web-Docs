# ProteinMPNN

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein sequences conditioned on input 3D structures using the ProteinMPNN family of models. Supports vanilla (all-atom), CA-only, and soluble-only model variants, with controls for sampling temperature, chain targeting, and residue omissions. Can run in production against the service, use mock outputs, or a quick-test mode.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/sequence-prediction/saasproteinmpnnnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to design sequences for one or more protein backbones provided as PDB structures. Choose an appropriate ProteinMPNN model variant, set the number of sequences to generate per target, and tune sampling temperatures to balance diversity and determinism. Optionally restrict design to specific chains and omit certain amino acids; then feed the resulting FASTA sequences into downstream analysis, scoring, or structure prediction steps.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Protein structure(s) to design against. Provide one or more PDB entries; each will be used as a design target.</td><td style="word-wrap: break-word;">{'1ABC': 'ATOM ...\nTER\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">['vanilla_v_48_002', 'vanilla_v_48_010', 'vanilla_v_48_020', 'vanilla_v_48_030', 'ca_only_v_48_002', 'ca_only_v_48_010', 'ca_only_v_48_020', 'soluble_v_48_002', 'soluble_v_48_010', 'soluble_v_48_020', 'soluble_v_48_030']</td><td style="word-wrap: break-word;">ProteinMPNN model variant to use. Vanilla uses all atoms, CA-only expects CA-only structures, and soluble variants are trained on soluble proteins.</td><td style="word-wrap: break-word;">vanilla_v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise applied to backbone atoms to encourage sequence diversity.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences to generate per input structure.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum allowed sequence length. Designs exceeding this length will be limited.</td><td style="word-wrap: break-word;">200000</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated temperatures controlling amino-acid sampling (e.g., 0.1, 0.2, 0.3). Higher values increase diversity.</td><td style="word-wrap: break-word;">0.1, 0.15, 0.2</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated amino acids to exclude from designs (single-letter codes).</td><td style="word-wrap: break-word;">C, M, W</td></tr>
<tr><td style="word-wrap: break-word;">chain_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to design. If empty, all chains in each structure are designed.</td><td style="word-wrap: break-word;">A, B</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducible sampling.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">['MOCK', 'PROD', 'TEST']</td><td style="word-wrap: break-word;">Run mode. MOCK returns predefined sample data, PROD queries the live service, TEST runs with minimal parameters for quick checks.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">All generated sequences in FASTA format, aggregated over all input structures and requested samples.</td><td style="word-wrap: break-word;">>1ABC_design_0 MKTAYIAKQRQISFVKSHFSRQ... >1ABC_design_1 GHHYVKDLAKELGITKRGL...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model selection**: Use CA-only models for CA-only PDBs, soluble models for soluble-focused designs; otherwise choose a vanilla model.
- **Model name format**: The model_name must match one of the provided options; invalid names will raise an error.
- **Sampling temperatures**: Provide at least one value; empty input is not allowed. Include values such as 0.1, 0.15, 0.2, 0.25 to balance quality and diversity.
- **Chain targeting**: Leave chain_list empty to design all chains; specifying chains restricts design to those IDs.
- **Residue omissions**: Ensure omit_amino_acids does not exclude too many residues for the target; overly restrictive sets can yield poor or no designs.
- **TEST mode behavior**: In TEST mode, num_seq_per_target is forced to 1 to speed up validation runs.
- **Throughput considerations**: Generating many sequences or processing multiple structures can increase runtime; adjust num_seq_per_target accordingly.
- **Reproducibility**: Using the same seed and inputs yields consistent results, subject to stochastic elements inherent to sampling.

## Troubleshooting
- **Invalid model_name error**: Select a model from the provided list (e.g., vanilla_v_48_020) and ensure the exact spelling.
- **Empty sampling_temp**: Provide at least one temperature value such as 0.1; use comma separation for multiple values.
- **No sequences returned**: Verify chain_list matches chains present in the PDB, reduce omit_amino_acids restrictions, and confirm the PDB files are valid.
- **Mismatched structure/model**: If using a CA-only structure with a vanilla model (or vice versa), switch to the appropriate model variant.
- **Excessive runtime or timeouts**: Reduce num_seq_per_target, limit the number of input structures, or use TEST mode to validate your setup.
- **Unexpected diversity/quality**: Lower sampling_temp for more deterministic, higher-quality sequences; increase for more diversity.
- **Length limit issues**: If designs are truncated or rejected, increase max_length or provide structures with fewer residues.
- **Reproducibility concerns**: Ensure the same seed and identical inputs; changes to temperatures, noise, or chains will affect outputs.
