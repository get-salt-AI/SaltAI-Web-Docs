# Evaluate Prot Param

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates physicochemical and sequence-derived parameters for one or more protein structures and returns a consolidated CSV. It computes protein charge at specified pH values and includes additional protein/sequence metrics. Supports three modes: PROD (service-backed), MOCK (predefined data), and TEST (minimal, faster evaluation).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Functional Prediction/EvaluateProtParamNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after loading or generating protein structures to obtain a tabulated summary of properties, including charge at chosen pH values. Typical workflow: provide a set of PDB structures and a comma-separated list of pH values; consume the resulting CSV for ranking, filtering, or downstream analyses.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Collection of named protein structures to evaluate. Each entry should be a name mapped to its PDB text content.</td><td style="word-wrap: break-word;">{"design_A": "ATOM ...\nEND", "design_B": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pH_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of pH values (floats) at which to compute protein charge. Parsed into a float list internally.</td><td style="word-wrap: break-word;">6.5, 7, 8.0</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE[MOCK, PROD, TEST]</td><td style="word-wrap: break-word;">Execution mode. MOCK returns static demo output; PROD runs the live evaluation; TEST limits scope for a faster check.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">score.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">CSV summarizing evaluated parameters per structure, including charge at requested pH values and other protein/sequence metrics.</td><td style="word-wrap: break-word;">name,seq_len,gravy,instability_index,isoelectric_point,charge@pH6.5,charge@pH7,charge@pH8\ndesign_A,137,-0.42,32.1,6.8,-1.2,-2.0,-3.5\ndesign_B,141,-0.30,28.7,7.1,0.5,-0.3,-1.8</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Mode behavior: PROD calls the live evaluation service; MOCK loads predefined results; TEST uses only the first structure and sets pH_list to [7] for speed.
- pH parsing: pH_list must be numeric and comma-separated (e.g., "6.0,7,8.5"); spaces are allowed.
- Batch handling: Multiple PDB inputs are supported; the CSV will contain rows for each structure.
- Timeout scaling: Service timeout scales with the number of structures (about 300 seconds per structure). Large batches may run longer.
- Columns may vary: The exact set of metrics can evolve with the backing service; rely on the CSV headers returned at runtime.

## Troubleshooting
- Invalid pH values: Ensure pH_list contains only numbers separated by commas (e.g., "6.5,7,8").
- Only one row returned: In TEST mode, the node uses only the first structure and forces pH to 7.
- Long runtimes or timeouts: Reduce the number of structures, try TEST mode for a quick sanity check, or run smaller batches.
- Unexpected columns: Check the header row in the returned CSV; metrics may change with service updates.
- Demo-like output: MOCK mode serves static sample data; switch to PROD for real evaluations.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Functional Prediction/EvaluateProtParamNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

