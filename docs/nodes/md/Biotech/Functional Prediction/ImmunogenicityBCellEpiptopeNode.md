# Immunogenicity B-Cell Epiptope

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Predicts B-cell epitope immunogenicity for one or more protein structures using a sliding window across the sequence. It summarizes predicted epitope propensities into a CSV table for downstream analysis. Supports production evaluation, a fast test mode, and a mock mode with precomputed data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Functional Prediction/ImmunogenicityBCellEpiptopeNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to assess potential B-cell epitope regions on designed or candidate protein structures. Typical workflow: generate or load PDB structures, run this node to scan for epitope propensity with a chosen window size, and use the resulting CSV to compare immunogenicity across designs or to guide sequence/structure refinement.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more protein structures to evaluate. Provide a mapping from a unique name to the PDB content.</td><td style="word-wrap: break-word;">{"design_1": "ATOM ...\nEND", "design_2": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">window_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sliding window length used to scan the sequence for B-cell epitope propensities. Larger windows smooth local variation; smaller windows give finer granularity.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">["MOCK", "PROD", "TEST"]</td><td style="word-wrap: break-word;">Execution mode. PROD runs the full evaluation, MOCK returns precomputed example results, TEST runs a faster, minimal evaluation (may limit inputs).</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">score.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">CSV table of B-cell epitope predictions for the provided proteins. Contains rows for each protein (and sliding window region) with corresponding scores.</td><td style="word-wrap: break-word;">protein,window_start,window_end,epitope_score\ndesign_1,1,10,0.42\ndesign_1,2,11,0.37</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The window_size strongly affects sensitivity and smoothness of predictions; adjust based on protein length and desired resolution.
- In TEST mode, the node may limit evaluation to the first provided structure to speed up runtime.
- MOCK mode returns precomputed example results and is intended for UI testing and demonstrations, not scientific conclusions.
- If multiple structures are provided, the output CSV will contain aggregated rows for all inputs.
- Ensure input PDBs are valid protein structures; malformed or non-protein content can lead to failed or meaningless predictions.

## Troubleshooting
- No or empty results: Verify the pdb input contains at least one valid structure and that window_size is a positive integer.
- Unexpectedly few rows: In TEST mode, only the first structure may be evaluated. Switch to PROD mode or reduce the number of inputs.
- CSV appears malformed: Check for special characters or commas in protein names. Use simple alphanumeric identifiers for pdb keys.
- Long runtimes: Reduce the number of input structures, use a larger window_size, or switch to TEST mode to validate configuration before full runs.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Functional Prediction/ImmunogenicityBCellEpiptopeNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

