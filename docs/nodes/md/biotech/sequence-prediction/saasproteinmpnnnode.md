# ProteinMPNN

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node submits protein backbone structures (as PDB data) to a remote ProteinMPNN SaaS service to design compatible amino acid sequences. It supports multiple model variants (vanilla, CA-only, and soluble), configurable sampling temperatures, and backbone noise to balance stability and diversity. The node returns all generated sequences in FASTA format and supports production, mock, and test execution modes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/sequence-prediction/saasproteinmpnnnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have one or more protein backbone structures and want to design plausible amino acid sequences that fit those backbones. Typically, an upstream node predicts or prepares structures and outputs a PDB mapping that you feed into this node as `pdb`. Downstream, you can send the resulting FASTA sequences to nodes that predict structure of designs, compute biophysical properties, or export sequences for further experimental or computational work. Select `model_name` based on your backbone input: choose a `vanilla_*` model for full-atom structures, `ca_only_*` for CA-only backbones, and `soluble_*` for design focusing on soluble proteins. Adjust `sampling_temp` to control exploration of sequence space (higher values lead to more diversity) and use `backbone_noise` to further increase variety. Use `omit_amino_acids` to enforce residue-level constraints (for example to avoid cysteines) and `chain_list` to focus design on specific chains of a multimeric complex. During prototyping, prefer TEST mode to limit the number of sequences, and during demonstrations or offline usage, MOCK mode uses bundled example data without accessing the remote service; switch to PROD mode for real design runs.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary-like mapping from structure identifiers to PDB-formatted strings representing protein backbones to be used as design templates. Each entry should be valid PDB text, with appropriate chain IDs; CA-only structures are allowed when using the corresponding ca_only models.</td><td style="word-wrap: break-word;">{"design_target_1": "HEADER\nATOM      1  N   ALA A   1 ...\nEND\n", "design_target_2": "HEADER\nATOM      1  N   GLY B   1 ...\nEND\n"}</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Name of the ProteinMPNN model variant to use. Must be one of: "vanilla_v_48_002", "vanilla_v_48_010", "vanilla_v_48_020", "vanilla_v_48_030", "ca_only_v_48_002", "ca_only_v_48_010", "ca_only_v_48_020", "soluble_v_48_002", "soluble_v_48_010", "soluble_v_48_020", "soluble_v_48_030". The prefix (vanilla, ca_only, soluble) controls how structures are interpreted and what they were trained on.</td><td style="word-wrap: break-word;">vanilla_v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise added to backbone atoms before sequence generation. Allowed values are between 0.0 and 1.0. Use higher values when you want more diverse sequences at the cost of tighter structural fit.</td><td style="word-wrap: break-word;">0.2</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences to generate for each input PDB structure. Minimum is 1. In TEST mode, this value is overridden internally to 1 regardless of the provided value, to keep runs fast.</td><td style="word-wrap: break-word;">16</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum allowed sequence length for each design. Must be between 1 and 1000000. This should exceed the length of your backbones; if set too low, designs for long proteins will fail or be truncated upstream.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of sampling temperatures for amino acid selection, for example "0.1,0.15,0.2". Each token is parsed as a float and forwarded as a space-separated string to the backend. At least one valid numeric value is required; higher temperatures produce more diverse sequences.</td><td style="word-wrap: break-word;">0.1,0.15,0.2,0.25</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Random seed used to control stochastic elements of ProteinMPNN sampling. Valid range is 0 to 4294967295. Stored into output metadata to support reproducible re-runs.</td><td style="word-wrap: break-word;">123456</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Execution mode for the node. "MOCK" uses static mock data from a bundled JSON file and does not contact the remote service. "PROD" calls the live SaaS ProteinMPNN backend. "TEST" also calls the live backend but automatically sets `num_seq_per_target` to 1 to reduce cost and latency.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of one-letter amino acid codes to exclude from generated sequences. Parsed, stripped, and concatenated into a string that is passed as omit_AAs to the service. Default is "X" to avoid ambiguous residues; you might use values like "C" to avoid cysteines or "M,W" to exclude specific residues.</td><td style="word-wrap: break-word;">C,M,W</td></tr>
<tr><td style="word-wrap: break-word;">chain_list</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain identifiers to design (for example, "A,B"). Parsed and joined into a space-separated string for the backend. If left empty, all chains in each PDB are eligible for design.</td><td style="word-wrap: break-word;">A,B</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA-formatted text containing all protein sequences generated for the provided PDB backbones. Multiple sequences and structures are concatenated; headers indicate the originating backbone and sampling conditions. Metadata including the configuration and seed is attached to this output for traceability.</td><td style="word-wrap: break-word;">>design_target_1_temp0.1_seq0 MKTFFALVVASLLLAASA...GQ >design_target_1_temp0.2_seq1 MKTYFALIVASLLLAASA...DQ >design_target_2_temp0.1_seq0 GQNVLQGKKVAVILGASG...LL </td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Execution time grows with the number of input PDBs and the `num_seq_per_target` setting; a per-PDB timeout of roughly 300 seconds is used, so very large jobs may be slow or time out.
- **Performance**: TEST mode overrides `num_seq_per_target` to 1 for all inputs, which is ideal for quick debugging but not representative of full production workloads.
- **Limitations**: The `model_name` must strictly follow the expected pattern (prefix plus `v_48_XXX` version); an invalid string raises a ValueError before any remote call is made.
- **Limitations**: The `sampling_temp` field must yield at least one valid numeric temperature after parsing; providing an empty string or only delimiters causes a validation error requiring a non-empty temperature list.
- **Behavior**: In MOCK mode, outputs are constructed from a static mock JSON file by replacing a placeholder backbone identifier with actual PDB keys, so sequences do not reflect real designs for your structures.
- **Behavior**: The node normalizes comma-separated text inputs (`sampling_temp`, `omit_amino_acids`, and `chain_list`) into formats expected by the backend; malformed tokens can still cause parsing or service-side validation errors.

## Troubleshooting
- **Common Error 1**: Error mentioning an invalid model name – Confirm that `model_name` is exactly one of the allowed values (for example, "vanilla_v_48_020" or "soluble_v_48_010") and includes the `v_48_XXX` suffix.
- **Common Error 2**: Validation error about sampling temperature being empty – Ensure `sampling_temp` is not blank and that it contains at least one numeric value separated by commas, such as "0.1" or "0.1,0.15".
- **Common Error 3**: Long runtime or timeout – If node execution is very slow or times out, reduce `num_seq_per_target`, split a large set of PDBs into smaller batches, or start with TEST mode to verify configuration before running larger PROD jobs.
- **Common Error 4**: Backend rejects structure or chain configuration – Check that your PDB strings are syntactically valid and appropriate for the chosen model (for example, use `ca_only_*` for CA-only backbones) and that `chain_list` only refers to chains actually present in each PDB.
