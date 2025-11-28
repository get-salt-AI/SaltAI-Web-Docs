# ProteinMPNN

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein sequences conditioned on provided backbone structures using the ProteinMPNN family of models. Supports vanilla (all-atom), CA-only, and soluble-only model variants, with controls for backbone noise, temperature sampling, chain targeting, and amino-acid omissions. Can run in production, mock, or test modes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/sequence-prediction/saasproteinmpnnnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to design sequences for one or more protein backbones. Provide PDB structures, select a ProteinMPNN variant, and configure sequence sampling parameters. Typical workflow: prepare/clean PDBs, optionally select specific chains to design, set temperature(s) for diversity, and run in PROD for real results, TEST for quick sanity checks (1 sequence per target), or MOCK for predefined outputs.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Protein backbone structure(s) to design against. Provide one or more structures in PDB format; each entry corresponds to a target design.</td><td style="word-wrap: break-word;">{'my_design_1': 'ATOM ...\nTER\nEND\n', 'my_design_2': 'ATOM ...\nTER\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">ProteinMPNN model variant. Prefix determines mode (vanilla, ca_only, soluble) and suffix selects the model ID. Valid options: vanilla_v_48_002/010/020/030, ca_only_v_48_002/010/020, soluble_v_48_002/010/020/030.</td><td style="word-wrap: break-word;">vanilla_v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise applied to backbone atoms to induce diversity.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences to generate per input structure.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum allowed sequence length for design.</td><td style="word-wrap: break-word;">200000</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of temperatures used during amino acid sampling. Higher values increase diversity. Must not be empty.</td><td style="word-wrap: break-word;">0.1,0.15,0.2</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated one-letter codes for amino acids to exclude from designs.</td><td style="word-wrap: break-word;">C,M,W</td></tr>
<tr><td style="word-wrap: break-word;">chain_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to design. If empty, all chains are designed.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. MOCK returns predefined results, PROD calls the live service, TEST runs quickly with minimal settings (forces 1 sequence per target).</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">All generated sequences in FASTA format aggregated across inputs and samples.</td><td style="word-wrap: break-word;">>my_design_1_seq_0001 MKT...LL >my_design_1_seq_0002 GAS...VV </td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model selection**: The model_name must contain a valid prefix and an ID (e.g., vanilla_v_48_020). Prefix controls behavior (vanilla, ca_only, soluble).
- **Sampling temperatures**: sampling_temp must be a non-empty, comma-separated list (e.g., 0.1,0.2). Empty values will cause an error.
- **Chain targeting**: Leave chain_list empty to design all chains; otherwise provide comma-separated chain IDs (e.g., A,B).
- **Amino acid omissions**: Use one-letter codes in omit_amino_acids to exclude residues from designs.
- **TEST mode**: Automatically overrides num_seq_per_target to 1 for faster runs.
- **Multiple structures**: You can submit multiple PDBs in one run; timeouts scale with the number of inputs.
- **Output**: Only the concatenated FASTA of all sequences is returned.

## Troubleshooting
- **Invalid model name**: Ensure model_name matches one of the supported options (e.g., vanilla_v_48_020).
- **Empty sampling_temp**: Provide at least one temperature (e.g., 0.1). The field cannot be blank.
- **Chain IDs not found**: Verify chain_list matches chains present in your PDBs or leave it empty to design all chains.
- **Excessive sequence length**: If designs fail due to limits, reduce max_length or split very large structures.
- **Unexpected residues**: Add them to omit_amino_acids (e.g., C) if certain residues should not appear.
- **Reproducibility issues**: Set seed to a fixed integer. Note that higher backbone_noise or multiple temperatures increase variability.
- **Mock/test differences**: MOCK returns predefined data; TEST limits output to 1 sequence per target; use PROD for real generation.
