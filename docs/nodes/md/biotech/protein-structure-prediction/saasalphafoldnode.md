# Alphafold

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The SaaSAlphafoldNode (Alphafold) node takes multiple-sequence alignment (MSA) results in A3M format and runs a SaaS-hosted Alphafold pipeline. It converts MSAs to features, calls remote CPU and GPU services, and returns ranked protein structure predictions as PDB strings. It supports mock/test modes, model presets, model skipping, and deterministic seeding for reproducible predictions.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasalphafoldnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have computed MSAs for your protein sequences, typically via the SaaSMSASearchNode (MSA Search). It expects an A3M MSA dict keyed by sequence identifiers and will produce a dict of PDB-formatted structures for each sequence, with keys combining the original sequence ID and Alphafold model rank (for example, "seq1_model_1"). Place this node in the middle or later part of a biotech workflow: upstream nodes usually provide FASTA sequences (such as from file import or prompt-based design), followed by SaaSMSASearchNode to generate MSAs; downstream nodes might consume the PDB dict for structural analysis, visualization, stability scoring, docking, or design iterations.

Common integration patterns:
- FASTA import → SaaSMSASearchNode (MSA Search) → SaaSAlphafoldNode (Alphafold) → structure analysis/visualization nodes.
- Iterative protein design where you vary sequence, re-run MSA search, then call Alphafold to compare structures.

Best practices:
- Use PROD mode for real predictions, TEST mode only for quick pipeline checks on small or toy data, and MOCK for offline debugging of workflow logic.
- Set model_preset to "monomer" or "monomer_ptm" for single-chain targets; multimer is currently not supported and will raise an error.
- Use skip_models to control runtime versus accuracy (fewer models is faster, more models is more robust but slower) but ensure at least one model is left.
- Keep track of the output keys ("<fasta_id>_<pdb_name>") when connecting downstream nodes so you can reference the correct structure.

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
<tr><td style="word-wrap: break-word;">a3m</td><td>True</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Multiple-sequence alignment results in A3M format, provided as a mapping from sequence IDs to A3M content. Each value should be a valid A3M text block including the query and homologous sequences. Typically produced by SaaSMSASearchNode. Keys (IDs) must be consistent and unique; they will be used to label outputs and must match the FASTA IDs implied by the A3M content.</td><td style="word-wrap: break-word;">{   "BRCA1_HUMAN": ">BRCA1_HUMAN\nMSSSQE...KQL\n>homolog_1\nMSTSQE...KQL\n>homolog_2\nMSTSEE...KQM\n",   "P53_HUMAN": ">P53_HUMAN\nMEEPQSDPSV...\n>homolog_1\nMEEPQGDPSV...\n" }</td></tr>
<tr><td style="word-wrap: break-word;">search_templates</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to perform a template search before prediction. When true, Alphafold will try to find structural templates, which can improve accuracy but also increases runtime and resource usage. In TEST mode this is automatically forced to false for speed.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">model_preset</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: monomer, monomer_ptm, monomer_casp14, multimer)</td><td style="word-wrap: break-word;">Selects which Alphafold model configuration to use. Supported are monomer, monomer_ptm, and monomer_casp14; choosing multimer is not currently supported and will raise an error. Use monomer for most single-chain proteins, monomer_ptm if you care about predicted TM-score calibration, and monomer_casp14 for the original CASP14-like setup.</td><td style="word-wrap: break-word;">monomer_ptm</td></tr>
<tr><td style="word-wrap: break-word;">models_to_relax</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: NONE, BEST, ALL)</td><td style="word-wrap: break-word;">Controls whether to run the optional relaxation step on the predicted structures. In this implementation, any value other than NONE is not supported yet and will raise a NotImplementedError. Always leave this as NONE unless the platform explicitly announces support.</td><td style="word-wrap: break-word;">NONE</td></tr>
<tr><td style="word-wrap: break-word;">enable_gpu_relax</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If relaxation were supported, this would toggle using GPU versus CPU for that step. Currently kept for forward compatibility; setting it has no effect because models_to_relax must be NONE for successful execution.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">skip_models</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of model indices (1–5) to skip during prediction. Values must be integers in the range 1–5, and you cannot skip all five models (that will cause an error). The node deduplicates and validates entries internally. In TEST mode this is overridden to skip models [2,3,4,5], leaving only the first model.</td><td style="word-wrap: break-word;">2,4</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed (0 to 4294967295) used for Alphafold’s stochastic components. For batched predictions, each sequence uses an incremented seed (seed + index), enabling reproducibility across runs as long as inputs and configuration remain identical.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: MOCK, PROD, TEST)</td><td style="word-wrap: break-word;">Execution mode. PROD calls the actual SaaS Alphafold service. MOCK loads precomputed local JSON results for both feature generation and prediction, useful for debugging workflow wiring without remote calls. TEST uses minimal parameters for faster runs (forces single model and disables template search) and is intended for quick validation, not final scientific results.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A dictionary mapping combined identifiers to predicted protein structures in PDB format. Keys are built as "<fasta_id>_<pdb_name>" where <pdb_name> encodes the model and/or rank assigned by Alphafold (for example, "model_1", "model_2"). Values are full PDB file contents for each ranked structure. Downstream nodes can select specific keys (such as the top-ranked model) for visualization, stability scoring, or docking workflows.</td><td style="word-wrap: break-word;">{   "BRCA1_HUMAN_model_1": "HEADER    PREDICTED STRUCTURE BRCA1_HUMAN\nATOM      1  N   MET A   1  ...\nTER\nEND\n",   "BRCA1_HUMAN_model_2": "HEADER    PREDICTED STRUCTURE BRCA1_HUMAN\nATOM      1  N   MET A   1  ...\nTER\nEND\n",   "P53_HUMAN_model_1": "HEADER    PREDICTED STRUCTURE P53_HUMAN\nATOM      1  N   MET A   1  ...\nTER\nEND\n" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Runtime depends heavily on the number of sequences in `a3m`, the chosen model_preset, and which models are skipped. Each sequence triggers two remote calls (feature combination and Alphafold prediction) and the prediction step can take minutes per sequence in PROD mode.
- **Limitations**: Structural relaxation (models_to_relax different from "NONE") and multimer model_preset are not supported and will immediately raise errors. Use only monomer-type presets with models_to_relax set to NONE.
- **Behavior**: In TEST mode the node silently overrides `skip_models` to [2,3,4,5] and `search_templates` to false, which changes the effective configuration versus what is shown in the user interface; do not rely on TEST mode results for final scientific conclusions.
- **Behavior**: The node converts A3M inputs to Stockholm format and reconstructs FASTA sequences internally; if there is a mismatch between inferred FASTA IDs and MSA keys, it will raise a validation error instead of producing outputs.
- **Performance**: For batched inputs, the seed is incremented per sequence (seed + index), allowing reproducible but distinct stochastic runs across multiple sequences in one call.

## Troubleshooting
- **Invalid skip_models**: An error such as "Expected models indices in `skip_models` to be in range [1, 5] but got [...]" or "Cannot skip all 5 models" indicates the comma-separated list is out of range or covers all models. Fix by providing indices only from 1–5 and ensuring at least one model is not skipped (for example, "2,4" instead of "1,2,3,4,5").
- **Unsupported relaxation**: If you see "Relaxation is not supported yet, please contact us if this feature is required.", you have set models_to_relax to BEST or ALL. Change models_to_relax to NONE and re-run the node.
- **Unsupported multimer preset**: The error "Multimer model is not supported yet, please contact us if this feature is required." means model_preset was set to multimer. Switch to monomer, monomer_ptm, or monomer_casp14.
- **Key mismatch between FASTA and features**: An error like "Expected fastas IDs to match features IDs but got {...} != {...}" indicates the derived FASTA IDs from A3M content do not match the alignment keys. Ensure your upstream SaaSMSASearchNode produced consistent IDs and that you did not manually alter the A3M dict keys.
- **Timeouts or no response from service**: If the node hangs or fails without local validation errors, the underlying SaaS service may have timed out or be unavailable. Try reducing the number of sequences, using TEST mode for debugging, or running with fewer models (via skip_models) before retrying in PROD.
