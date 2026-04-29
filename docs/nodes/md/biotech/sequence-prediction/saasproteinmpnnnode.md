# ProteinMPNN

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes one or more protein backbone structures (PDB) and uses the ProteinMPNN model family to design compatible amino-acid sequences. It supports multiple model variants (vanilla, CA-only, soluble), temperature-controlled sampling, backbone noise, residue exclusion, and chain selection. Computation is executed by a Salt SaaS biotech GPU service, with MOCK and TEST modes for fast or offline-style runs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/sequence-prediction/saasproteinmpnnnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have 3D protein backbones and need de novo or redesign sequences that are compatible with those structures. Typical workflows feed PDB structures from structure prediction nodes (for example, AlphaFold-like prediction nodes), structure refinement nodes, or PDB upload/import nodes into this node, then send the designed sequences into downstream sequence analysis, stability scoring, binding prediction, or sequence filtering nodes. Choose `model_name` according to your input: vanilla_* for full-atom structures, ca_only_* for CA-only backbones, and soluble_* when you want designs biased toward soluble proteins. Adjust `sampling_temp` to balance diversity and determinism (multiple comma-separated temperatures allowed), and use `omit_amino_acids` to remove undesired residues (such as C to avoid disulfides). Use `chain_list` to design only specific chains in complexes. For quick checks or CI, run with `mode = TEST` to get exactly one sequence per target, or `mode = MOCK` to rely on bundled mock outputs without contacting the remote service. For real designs, use `mode = PROD`, which calls the salt-saas-biotech-gpu-proteinmpnn backend and returns model-generated FASTA sequences enriched with metadata.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Collection of one or more protein structures to design against. It is a mapping from target name to PDB-formatted string, where each value contains a full-atom or CA-only backbone consistent with the selected model. The total residue count per target must not exceed `max_length`.</td><td style="word-wrap: break-word;">{'design_target_1': 'HEADER    EXAMPLE PDB\\nATOM      1  N   MET A   1      ...\\nATOM      2  CA  MET A   1      ...\\nEND\\n', 'design_target_2': 'HEADER    SECOND TARGET\\nATOM      1  N   GLY A   1      ...\\nEND\\n'}</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">ProteinMPNN model variant. Must be one of the allowed names: vanilla_v_48_002, vanilla_v_48_010, vanilla_v_48_020, vanilla_v_48_030, ca_only_v_48_002, ca_only_v_48_010, ca_only_v_48_020, soluble_v_48_002, soluble_v_48_010, soluble_v_48_020, soluble_v_48_030. vanilla_* uses all atoms, ca_only_* expects CA-only backbones, and soluble_* is trained on soluble protein sets.</td><td style="word-wrap: break-word;">vanilla_v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise added to backbone atoms before design, in the range 0.0–1.0. Increasing this can encourage robustness and diversity but too large values may degrade structural compatibility.</td><td style="word-wrap: break-word;">0.2</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences to generate for each PDB target. Must be at least 1. In TEST mode this is automatically overridden to 1 internally, regardless of the value you set.</td><td style="word-wrap: break-word;">32</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum allowed total sequence length (number of residues) per design target. Valid range is 1–1000000 (default 200000). Very large complexes must fit under this bound or may be rejected or cause timeouts in the backend service.</td><td style="word-wrap: break-word;">5000</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of sampling temperatures used by ProteinMPNN for residue sampling, for example "0.1,0.15,0.2". These are parsed into floats and passed as a space-separated string. After parsing, the list must not be empty; at least one numeric temperature is required. Higher temperatures generally increase design diversity.</td><td style="word-wrap: break-word;">0.1,0.15,0.2,0.25</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed (0–4294967295) controlling stochastic sampling. Use a fixed seed to reproduce results with the same inputs and parameters, or vary it to explore alternate designs.</td><td style="word-wrap: break-word;">12345</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Execution mode. PROD sends a real request to the salt-saas-biotech-gpu-proteinmpnn service and returns live model predictions. MOCK loads a static mock_data/proteinmpnn.json file and relabels sequences per PDB, ignoring your structures and most parameters. TEST behaves like PROD but forces num_seq_per_target to 1 for quick turnaround.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of amino acid one-letter codes to exclude from designs, for example "C,W". Parsed and joined into a single no-space string (e.g. "CW") before sending to the service. Default is "X", which blocks ambiguous residues. Leave empty to allow all residues.</td><td style="word-wrap: break-word;">C,M,W</td></tr>
<tr><td style="word-wrap: break-word;">chain_list</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain identifiers to design (for example "A,B"). If provided, only these chains will be redesigned; other chains are kept fixed. If empty, all chains in each PDB are eligible for design. Internally converted to a space-separated string such as "A B".</td><td style="word-wrap: break-word;">A,B</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Single FASTA-formatted blob containing all designed sequences for all input PDB targets. Each sequence is labeled to indicate the originating target and design index. The node also attaches metadata (such as seed and configuration) so downstream nodes can understand how the sequences were generated.</td><td style="word-wrap: break-word;">>design_target_1_seq0\nMKTAYIAKQRQISFVKSHFSRQDILD...\n>design_target_1_seq1\nGQSDKRQLDLATKQLSNAILENM...\n>design_target_2_seq0\nADKTRVLENVRGAPKVGIVDAN...\n</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Runtime scales with both the total residue count per PDB and num_seq_per_target. The node uses a timeout of 300 seconds multiplied by the number of PDB entries, so very large systems or high sequence counts may approach this limit.
- **Performance**: TEST mode is useful for rapid iterations because it overrides num_seq_per_target to 1, significantly reducing backend load and latency compared to full production runs.
- **Limitations**: model_name must conform exactly to the pattern '<prefix>_v_48_xxx' with prefix in {vanilla, ca_only, soluble}. If parsing fails, the node raises a ValueError without contacting the backend.
- **Limitations**: sampling_temp must yield at least one valid float after parsing the comma-separated string; an empty or malformed value causes an immediate validation error.
- **Behavior**: MOCK mode always uses precomputed mock_data/proteinmpnn.json outputs, simply replacing a placeholder target name with each PDB key. The results do not depend on your actual structures or most parameters.
- **Behavior**: Hidden workflow and user identifiers are passed through to the SaaS service for tracking but do not affect the designed sequences; changing them only influences logging and attribution.

## Troubleshooting
- **Invalid model name error**: If you encounter an error like "Invalid model name: {model_name}", ensure that you selected one of the supported options (for example, vanilla_v_48_020) and did not alter the version pattern or add extra text.
- **Sampling temperature validation failure**: If the node reports that sampling temperature cannot be empty, check that `sampling_temp` contains at least one numeric value, and that you used commas to separate entries (for example, "0.1,0.2"). Remove trailing commas or non-numeric tokens.
- **Long runtime or timeout**: If the node hangs or fails after several minutes in PROD or TEST mode, the backend may have exceeded its timeout due to large targets or many sequences. Reduce num_seq_per_target, split your complex into smaller units, or run fewer PDB targets per execution.
- **Unexpected or repeated MOCK outputs**: When running in MOCK mode, you might see identical or unrealistic sequences for different inputs because all outputs derive from a single mock file. Switch to PROD or TEST to obtain structure- and parameter-dependent designs.
