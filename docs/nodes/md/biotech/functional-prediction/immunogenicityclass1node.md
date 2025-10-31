# Immunogenicity Class 1

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Predicts MHC class I immunogenicity for input protein structures by scanning sequences with a sliding peptide window and scoring predicted binding to a specified class I allele. Returns a CSV table of immunogenicity scores for all peptides/windows per structure.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/immunogenicityclass1node.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to pre-screen designed proteins for potential MHC class I immunogenic peptides. Provide one or more PDB structures, select the target allele (e.g., HLA-A0101), and set the peptide window size (typically 8–11, default 9). Integrate it after structure generation/selection to filter candidates before downstream analysis or experimental validation.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more protein structures to evaluate. Each entry should be a named structure representing a single protein for which peptide immunogenicity will be assessed.</td><td style="word-wrap: break-word;">{'designA_ranked_0.pdb': 'PDB_STRING_CONTENT', 'designB_ranked_0.pdb': 'PDB_STRING_CONTENT'}</td></tr>
<tr><td style="word-wrap: break-word;">c_allele</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MHC class I allele against which immunogenicity (peptide binding) is predicted.</td><td style="word-wrap: break-word;">HLA-A0101</td></tr>
<tr><td style="word-wrap: break-word;">c_window_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sliding window size (peptide length) used to generate peptides for binding prediction. Class I peptides are typically 8–11 residues; default is 9.</td><td style="word-wrap: break-word;">9</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">['MOCK', 'PROD', 'TEST']</td><td style="word-wrap: break-word;">Execution mode. MOCK returns predefined example results, PROD runs the actual prediction service, TEST uses minimal parameters for quick validation (not biologically meaningful).</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">score.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">CSV table of immunogenicity scores for peptides derived from the input structures. Typically includes multiple rows per structure (one per peptide window) with scores and identifiers.</td><td style="word-wrap: break-word;">protein_id,peptide,start,end,allele,score example_protein,SIINFEKL,10,17,HLA-A0101,0.87</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Allele format**: For class I, use allele strings like HLA-A0101 (no colons).
- **Peptide length**: Class I predictions are most relevant for 8–11-mers; default is 9.
- **Multiple rows per structure**: Output contains one row per peptide window, so expect many rows per input protein.
- **Performance**: Processing time scales with the number of structures and the window size.
- **TEST mode behavior**: In TEST mode, window size is automatically reduced to 3 and only the first structure is processed. Results are for validation only.
- **MOCK mode**: Returns canned data useful for UI and pipeline testing; not for scientific interpretation.

## Troubleshooting
- **Empty or missing scores**: Verify that the PDB inputs contain valid, parseable polypeptide chains and that c_window_size is appropriate (e.g., 9).
- **Unexpectedly short peptides**: Ensure mode is set to PROD (not TEST), as TEST reduces peptide length for speed.
- **Unsupported or invalid allele**: If the run fails or returns errors, check that c_allele matches supported naming (e.g., HLA-A0101).
- **Timeouts with many inputs**: Reduce the number of structures or use a smaller window size, or run in batches to avoid timeouts.
- **CSV appears duplicated**: The output lists multiple peptide windows per protein. This is expected when scanning across the sequence.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/functional-prediction/immunogenicityclass1node/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

