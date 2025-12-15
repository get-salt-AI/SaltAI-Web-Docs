# ProteinMPNN

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein sequences conditioned on input protein backbones using the ProteinMPNN family of models. Supports vanilla, CA-only, and soluble-trained variants, with controls for backbone noise, sampling temperature, and residue/chain constraints. Can run against a remote SaaS service, use mock data, or a test mode that minimizes runtime.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/sequence-prediction/saasproteinmpnnnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have one or more protein structures and want to design compatible amino acid sequences. Provide PDB structures, choose a ProteinMPNN model variant, and tune parameters like sampling temperature and number of sequences per target. Integrate it after structure preparation and before downstream evaluation or scoring nodes that analyze or filter designed sequences.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Protein structure(s) to design against. Provide one or more PDB entries to generate sequences conditioned on their backbones.</td><td style="word-wrap: break-word;">{'design_1': '<pdb_text_block>', 'design_2': '<pdb_text_block>'}</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">ProteinMPNN model variant to use. Vanilla uses all atoms; CA-only parses CA-only structures; soluble uses weights trained on soluble proteins. Must match one of the supported names.</td><td style="word-wrap: break-word;">vanilla_v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise added to backbone atoms during design. Higher values can increase diversity.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences to generate per input structure.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum total sequence length permitted for processing.</td><td style="word-wrap: break-word;">200000</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated temperatures controlling sampling for amino acids. Higher values increase diversity. Will be parsed and applied internally.</td><td style="word-wrap: break-word;">0.1,0.15,0.2,0.25,0.3</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated amino acids to forbid in generated sequences. Use single-letter codes.</td><td style="word-wrap: break-word;">C,M,W</td></tr>
<tr><td style="word-wrap: break-word;">chain_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chains to design. Leave empty to design all chains.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base seed used for stochastic components to enable reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. MOCK returns predefined results, PROD calls the live service, TEST minimizes runtime by reducing generated sequences.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">All generated sequences in FASTA format for all provided structures and temperatures.</td><td style="word-wrap: break-word;">>design_1_temp0.1_seq0 ACDEFGHIKLMNPQRSTVWY >design_1_temp0.2_seq1 ACDFFGHIKLMNPQKSTVWY</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model name must follow supported patterns, e.g., vanilla_v_48_002, ca_only_v_48_010, soluble_v_48_030. Invalid names will cause an error.
- In TEST mode, num_seq_per_target is forced to 1 to speed up execution.
- Sampling temperature cannot be empty; provide at least one value (e.g., 0.1).
- Leave chain_list empty to design all chains; otherwise specify chains as a comma-separated list (e.g., A,B).
- Use omit_amino_acids with single-letter codes (e.g., C,M). These residues will not appear in outputs.
- Backbone noise adds Gaussian jitter to backbone atoms; set to 0.0 to disable.
- MOCK mode returns deterministic, precomputed data useful for UI testing and workflow prototyping.

## Troubleshooting
- Invalid model name: Ensure model_name is one of the supported options (e.g., vanilla_v_48_020).
- Empty sampling_temp: Provide a comma-separated list such as 0.1 or 0.1,0.2,0.25.
- Unexpected amino acids appearing: Verify omit_amino_acids uses single-letter codes separated by commas and contains no spaces.
- No sequences for specific chains: If you intended to design only some chains, ensure chain_list is set (e.g., A,B). Leave empty to target all chains.
- Too few sequences in TEST mode: TEST forces num_seq_per_target to 1; switch to PROD or MOCK and adjust num_seq_per_target.
- Input size too large: Reduce max_length or split large targets if the combined sequence length approaches limits.
