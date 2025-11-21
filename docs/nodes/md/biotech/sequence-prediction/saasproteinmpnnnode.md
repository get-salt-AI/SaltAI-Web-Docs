# ProteinMPNN

Generates protein sequences conditioned on an input protein backbone structure using the ProteinMPNN family of models. Supports vanilla (all-atom), CA-only, and soluble-protein variants, with configurable sampling temperature, amino-acid omissions, chain targeting, and backbone noise. Can operate in MOCK (precomputed), PROD (service-backed), or TEST (fast minimal) modes.

## Usage

Use this node to design sequences for one or more input PDB backbones. Choose the appropriate ProteinMPNN variant (vanilla, ca_only, or soluble) and set sampling temperatures to control diversity. Provide optional constraints such as omitting specific amino acids or restricting design to certain chains. In larger workflows, feed a PDB dictionary into this node and route the resulting FASTA sequences to downstream evaluation or structure prediction.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to design against. Keys are structure identifiers (e.g., filenames or IDs), values are PDB text content.</td><td style="word-wrap: break-word;">{"targetA": "ATOM ...\nEND\n", "targetB": "ATOM ...\nEND\n"}</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">ProteinMPNN model variant to use. Vanilla models use all atoms; ca_only models expect CA-only structures; soluble models are trained on soluble proteins.</td><td style="word-wrap: break-word;">vanilla_v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise added to backbone atoms to encourage robustness and diversity.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences to generate per input structure.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum sequence length allowed for generated designs.</td><td style="word-wrap: break-word;">200000</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated temperatures controlling sampling diversity for amino acids. Higher values yield more diverse sequences.</td><td style="word-wrap: break-word;">0.1,0.15,0.2</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of one-letter amino acid codes to exclude from generation.</td><td style="word-wrap: break-word;">C,M,W</td></tr>
<tr><td style="word-wrap: break-word;">chain_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to design. If empty, all chains are designed.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducibility across sampling.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. MOCK: returns predefined mock outputs. PROD: runs the live service. TEST: runs quickly with minimal settings.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Generated protein sequences in FASTA format for all input structures and samples.</td><td style="word-wrap: break-word;">>targetA_design_0\nMKT...\n>targetA_design_1\nGAS...\n>targetB_design_0\nVLK...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model selection**: The model_name must be one of the supported variants (e.g., vanilla_v_48_002/010/020/030, ca_only_v_48_002/010/020, soluble_v_48_002/010/020/030). CA-only models require CA-only structures.
- **Sampling temperatures**: sampling_temp must not be empty. Provide a comma-separated list (e.g., 0.1,0.15,0.2).
- **Chain targeting**: Leave chain_list empty to design all chains; otherwise, specify chains as a comma-separated list (e.g., A,B).
- **Omitted residues**: omit_amino_acids uses one-letter codes; listed residues will not be sampled.
- **TEST mode behavior**: In TEST mode, num_seq_per_target is forced to 1 to speed up runs.
- **Timeout scaling**: Service timeout scales with the number of input structures; large pdb sets will take longer.
- **Backbone noise**: Increasing backbone_noise can improve robustness but may affect fit to the original backbone.
- **Sequence length**: Designs exceeding max_length will be disallowed.

## Troubleshooting
- **Invalid model name**: If you see an error about model_name, ensure it matches one of the provided options and formatting (e.g., vanilla_v_48_020).
- **Empty sampling_temp**: Provide at least one temperature (e.g., 0.1). Comma separation is required for multiple values.
- **Incorrect chain IDs**: If designs target wrong chains or fail, verify chain_list matches the chains present in the PDBs. Leave empty to design all.
- **CA-only mismatch**: Using a ca_only model with an all-atom PDB (or vice versa) can cause failures. Pick the model that matches your structure type.
- **Service timeout or no output**: For large pdb sets or high num_seq_per_target, increase patience or reduce inputs. Try TEST mode to validate configuration quickly.
- **Unexpected amino acids**: If disallowed residues appear, confirm omit_amino_acids uses correct one-letter codes separated by commas.
- **Fewer sequences than expected**: In TEST mode, output is intentionally limited to 1 per target. In other modes, check max_length and constraints that may limit sampling.
