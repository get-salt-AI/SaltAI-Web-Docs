# Haddock

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs protein–protein docking between a candidate structure and a single target structure using configurable HADDOCK parameters. It returns the best-scoring docked model, all generated docked models, and a score table summarizing docking metrics. Supports production runs, lightweight test runs, and mock outputs for rapid prototyping.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/haddocknode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to evaluate how a designed candidate protein docks to a known target. Typical workflow: load or generate PDBs for a candidate and a single target, specify interface residues and chains for both, optionally tune HADDOCK TOML settings, then run to obtain best and full docking results plus score statistics. Integrate upstream with structure generation/prediction and downstream with binding analysis or ranking/selection steps.

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
<tr><td style="word-wrap: break-word;">candidate_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more candidate protein structures to dock. Provide a dictionary mapping names to PDB text.</td><td style="word-wrap: break-word;">{'candidate_1.pdb': 'ATOM ... END', 'candidate_2.pdb': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">target_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Exactly one target protein structure to dock against. Provide a single-entry dictionary mapping name to PDB text.</td><td style="word-wrap: break-word;">{'target_A.pdb': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">docking_inputs_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HADDOCK configuration in TOML. Controls sampling, stages, clustering, and evaluation. In TEST mode, sampling is reduced automatically for speed.</td><td style="word-wrap: break-word;">run_dir = "" ncores = 56 mode = "local" [topoaa] autohis = true [rigidbody] sampling = 100 tolerance = 20 [clustfcc] min_population = 4 [seletopclusts] top_models = 10 [caprieval] [flexref] tolerance = 20 [emref] tolerance = 20 [clustfcc] [caprieval] </td></tr>
<tr><td style="word-wrap: break-word;">target_specs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated residue indices on the target that define the binding interface. Parsed into a list of integers.</td><td style="word-wrap: break-word;">463,464,465,466,467,468</td></tr>
<tr><td style="word-wrap: break-word;">candidate_specs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated residue indices on the candidate that define the binding interface. Parsed into a list of integers.</td><td style="word-wrap: break-word;">20,21,22,23,24,25</td></tr>
<tr><td style="word-wrap: break-word;">candidate_chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs in the candidate to consider for docking.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">target_chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs in the target to consider for docking.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">MOCK \| PROD \| TEST</td><td style="word-wrap: break-word;">Execution mode. MOCK returns predefined outputs, PROD performs full docking, TEST runs with minimal sampling for speed.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time allowed for the docking job in seconds.</td><td style="word-wrap: break-word;">4000</td></tr>
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
<tr><td style="word-wrap: break-word;">best.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Best-scoring docked model for each candidate input.</td><td style="word-wrap: break-word;">{'candidate_1.pdb': 'MODEL 1 ... ENDMDL'}</td></tr>
<tr><td style="word-wrap: break-word;">bound.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">All docked models generated for each candidate, keyed by candidate and model identifiers.</td><td style="word-wrap: break-word;">{'candidate_1.pdb_model_001': 'MODEL 1 ... ENDMDL', 'candidate_1.pdb_model_002': 'MODEL 2 ... ENDMDL'}</td></tr>
<tr><td style="word-wrap: break-word;">scores.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">Merged docking score table (e.g., CAPRI-style statistics) with candidate/model identifiers.</td><td style="word-wrap: break-word;">candidate,model,score,irmsd,fnat,lrmsd candidate_1.pdb,model_001,-112.3,2.1,0.45,5.3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only a single target structure is supported. Supplying multiple targets will raise an error.
- Interface residues (target_specs and candidate_specs) and chain IDs must match the numbering and chain labels in the provided PDBs.
- The docking_inputs_config must be valid TOML. Incorrect formatting will cause failures.
- In TEST mode, sampling is reduced automatically to speed up runs; results are for quick checks, not final evaluations.
- MOCK mode returns predefined sample outputs and is intended for demos or UI wiring, not scientific conclusions.
- Timeout applies per docking job; complex configurations and large search spaces may require increasing it.
- All outputs preserve input names to help trace candidates through downstream analysis.

## Troubleshooting
- Error: Multiple target structures provided. Resolution: Ensure target_pdb contains exactly one entry.
- Empty or invalid residue lists. Resolution: Provide comma-separated integers (e.g., 10,11,12) matching PDB residue numbering; avoid ranges or non-numeric tokens.
- Chain not found in PDB. Resolution: Verify candidate_chains and target_chains correspond to chain IDs in the PDB files.
- Invalid TOML configuration. Resolution: Validate docking_inputs_config with a TOML linter; check section names and parameters.
- Run stuck or times out. Resolution: Increase the timeout, reduce sampling in the TOML, or switch to TEST mode for quick validation.
- Unexpectedly few docked models. Resolution: Increase sampling parameters in the [rigidbody] or later stages within docking_inputs_config.
- Scores.csv missing or empty. Resolution: Ensure docking completed successfully and that evaluation sections (e.g., caprieval) are enabled in the TOML.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/functional-prediction/haddocknode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

