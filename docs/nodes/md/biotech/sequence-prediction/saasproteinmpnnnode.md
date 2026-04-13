# ProteinMPNN

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein sequences conditioned on input protein backbones using the ProteinMPNN family of models. Supports full-atom (vanilla), CA-only, and soluble-protein–specialized variants, with controls for sampling temperature, backbone noise, chain targeting, and reproducibility. Returns all generated sequences in a single FASTA output covering all requested designs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/sequence-prediction/saasproteinmpnnnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to redesign or generate sequences for one or more input protein structures. Provide PDB backbones and choose a ProteinMPNN variant that matches your structure type (vanilla for full-atom, ca_only for CA-only PDBs, soluble for soluble-focused design). Adjust sampling temperature to trade off between diversity and conservativeness, optionally omit specific amino acids, and restrict design to selected chains. Place this node after structure generation/refinement or as part of sequence optimization pipelines; consume the resulting FASTA in downstream analysis, scoring, or structure prediction steps.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more protein backbone structures to design against. Expects a mapping from user-defined target names to PDB text contents; each entry is treated as an independent design target.</td><td style="word-wrap: break-word;">{"my_target": "ATOM ... END"}</td></tr>
<tr><td style="word-wrap: break-word;">model_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the ProteinMPNN model variant to use. Vanilla models use all atoms, ca_only models accept CA-only structures, and soluble models bias toward soluble proteins. Must follow the supported naming pattern.</td><td style="word-wrap: break-word;">vanilla_v_48_020</td></tr>
<tr><td style="word-wrap: break-word;">backbone_noise</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Standard deviation of Gaussian noise added to backbone atoms before design. Used to encourage sequence diversity and robustness; typically in the range 0.0–1.0.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">num_seq_per_target</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of independent sequences to generate per input structure. The total number of output sequences scales with the number of PDB targets times this value (subject to TEST mode overrides).</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">max_length</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum allowed sequence length for designs. Acts as a guardrail for very large or pathological inputs; designs exceeding this threshold are not attempted.</td><td style="word-wrap: break-word;">200000</td></tr>
<tr><td style="word-wrap: break-word;">sampling_temp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of sampling temperatures used for amino-acid selection. Higher values increase diversity at the cost of fidelity. At least one numeric value is required.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">omit_amino_acids</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of amino acids to exclude from the designs, using one-letter codes (for example, "A,C"). These residues will not be sampled at any position.</td><td style="word-wrap: break-word;">X</td></tr>
<tr><td style="word-wrap: break-word;">chain_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to design within each PDB. When empty, all chains present in the structure are designed.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed used to initialize stochastic sampling. Use a fixed value to obtain reproducible designs across repeated runs (within the same configuration and mode).</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode for the managed service. MOCK returns bundled example results, PROD runs the full production service, and TEST runs quickly with constrained settings (such as forcing one sequence per target).</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA-formatted protein sequences generated for all input structures and requested samples. Each header typically encodes the source target name and design index.</td><td style="word-wrap: break-word;">>my_target_design_0 MADEUPSEQUENCE... >my_target_design_1 MADEUPSEQUENCE...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model naming**: model_name must match a supported pattern like <prefix>_v_48_<nnn>, where prefix is one of vanilla, ca_only, or soluble and the version suffix is a valid number (for example, v_48_002, v_48_010, v_48_020, v_48_030).
- **Mode TEST behavior**: When mode is set to TEST, the node forces num_seq_per_target to 1 to ensure fast, lightweight execution regardless of the requested value.
- **Sampling temperature requirement**: sampling_temp must contain at least one numeric value; leaving it empty or non-numeric will cause validation or runtime errors.
- **Chain targeting**: Use chain_list to focus design on specific chains; leaving it empty designs all chains in each PDB. Mis-specified chain IDs will result in those chains being ignored.
- **Structural compatibility**: Use ca_only models exclusively with CA-only PDB structures and vanilla models for full-atom inputs; mixing these can lead to failures or degraded results.
- **Scaling and timeouts**: Total runtime increases with the number of PDB targets, the requested num_seq_per_target, and the temperature schedule; very large batches may approach or exceed service timeouts.

## Troubleshooting
- **Invalid model name error**: Verify that model_name follows the pattern <prefix>_v_48_<nnn> with a supported prefix (vanilla, ca_only, soluble) and a valid version number, and correct any typos or unsupported variants.
- **Sampling temperature empty or invalid**: Ensure sampling_temp is a non-empty, comma-separated list of numeric values (for example, "0.1" or "0.2,0.5"); remove stray characters or spaces that prevent parsing.
- **Unexpected residues or missing exclusions**: Confirm omit_amino_acids is a comma-separated list of valid one-letter amino-acid codes (for example, "A,C"); remove spaces or invalid symbols that might cause the list to be ignored.
- **Design restricted to fewer chains than expected**: Check that chain_list correctly lists the intended chain IDs (for example, "A,B") and matches the chain identifiers present in the PDB; leave it empty to design all chains.
- **Long runtimes or timeouts**: Reduce num_seq_per_target, simplify the sampling_temp schedule (fewer or lower temperatures), or submit fewer PDB targets. Using TEST mode can help validate configuration quickly before full PROD runs.
