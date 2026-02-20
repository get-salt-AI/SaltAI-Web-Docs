# ProteinMPNN

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein sequences conditioned on input protein backbones using the ProteinMPNN family of models. Supports full-atom (vanilla), CA-only, and soluble-protein–specialized variants with controls for sampling temperature, backbone noise, chain targeting, and reproducibility. Returns all generated sequences in a single FASTA output.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/sequence-prediction/saasproteinmpnnnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to redesign sequences for one or more input protein structures. Provide PDB backbones and select a ProteinMPNN variant that matches your structure type (vanilla for full-atom, ca_only for CA-only PDBs, soluble for soluble-focused design). Tune sampling temperature for diversity, optionally omit specified amino acids, and target specific chains. Integrate after structure generation/refinement or for sequence optimization pipelines; the node outputs a consolidated FASTA with all sequences.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more protein structures to design against. Expects a mapping of names to PDB contents; each entry is treated as an independent target.</td><td style="word-wrap: break-word;">{"my_target": "ATOM ... END"}</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">ProteinMPNN model variant to use. Vanilla models use all atoms, CA-only models accept CA-only structures, and soluble models bias toward soluble proteins.</td><td style="word-wrap: break-word;">vanilla_v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise added to backbone atoms to promote sequence diversity and robustness. Range 0.0–1.0.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences to generate per input structure.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum allowed sequence length. Acts as a guardrail for very large designs.</td><td style="word-wrap: break-word;">200000</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated temperature schedule for sampling amino acids (e.g., "0.2, 0.25, 0.5"). Higher values increase diversity. At least one value is required.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of amino acids to prohibit during design (e.g., "A,C"). Use single-letter codes.</td><td style="word-wrap: break-word;">X</td></tr>
<tr><td style="word-wrap: break-word;">chain_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to design. Leave empty to design all chains.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducibility. Unsigned 32-bit range.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. MOCK returns bundled example results, PROD runs the managed service, TEST runs quickly with minimal outputs.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA-formatted sequences generated for all input structures and requested samples.</td><td style="word-wrap: break-word;">>my_target_design_0 MADEUPSEQUENCE... >my_target_design_1 MADEUPSEQUENCE...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model name must include a valid version suffix (e.g., v_48_002, v_48_010, v_48_020, v_48_030) and a supported prefix: vanilla, ca_only, or soluble.
- If mode is TEST, the node forces num_seq_per_target to 1 to ensure fast execution.
- Sampling temperature cannot be empty; at least one numeric value is required.
- Set chain_list to target only specific chains; leave empty to design all chains present in the PDB.
- Use ca_only models only with CA-only PDB inputs; use vanilla for full-atom PDBs.
- Large pdb sets scale the execution timeout with the number of targets.

## Troubleshooting
- Invalid model name error: Ensure model_name matches the pattern <prefix>_v_48_<nnn> where prefix is one of vanilla, ca_only, or soluble.
- Sampling temperature empty: Provide at least one value (e.g., "0.1"); use commas to specify a schedule.
- Unexpected residues or omissions: Verify omit_amino_acids is a comma-separated list of one-letter codes (e.g., "A,C").
- Design limited to some chains only: Confirm chain_list is set correctly (e.g., "A,B") or left empty to include all chains.
- Timeouts or long runtimes: Reduce num_seq_per_target, simplify sampling_temp, or submit fewer pdb targets at once.
