# Immunogenicity MHC 2

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Predicts MHC class II peptide-binding immunogenicity from provided protein structures. It scans each protein sequence using a sliding window to generate peptides, scores their predicted binding to a specified MHC-II allele, and returns a consolidated score table.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Functional Prediction/ImmunogenicityMHC2Node.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to assess potential MHC-II epitope immunogenicity for one or more protein structures. Provide one or more PDBs, choose an MHC-II allele and peptide window size, and run in PROD for real predictions, MOCK for quick demos, or TEST for faster limited runs. The output CSV can be used for downstream filtering, ranking, or annotation of candidate designs.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Protein structures to evaluate. Accepts one or more PDBs keyed by name.</td><td style="word-wrap: break-word;">{'candidate_A.pdb': 'ATOM ...', 'candidate_B.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">allele_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MHC class II allele to evaluate binding against.</td><td style="word-wrap: break-word;">DRB1*01:01</td></tr>
<tr><td style="word-wrap: break-word;">window_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sliding window length (peptide size) used to generate candidate epitopes from the sequence.</td><td style="word-wrap: break-word;">15</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">ENUM['MOCK','PROD','TEST']</td><td style="word-wrap: break-word;">Execution mode. MOCK uses predefined results for demonstration, PROD runs full prediction, TEST uses limited inputs for faster runs.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">score.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">Table of predicted MHC-II immunogenicity metrics for all peptides generated from each input structure.</td><td style="word-wrap: break-word;">protein,allele,window_size,start,end,peptide,score candidate_A.pdb,DRB1*01:01,15,5,19,ABCDE... ,0.87 candidate_A.pdb,DRB1*01:01,15,6,20,BCDEF... ,0.65 ...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Input PDB must be provided as a mapping from names to PDB contents; each entry is processed independently.
- Valid window_size range is 11 to 30; default is 15.
- allele_name should be a valid MHC-II allele (for example, DRB1*01:01).
- In TEST mode, processing is limited to the first provided structure to speed up execution. The window_size is not automatically reduced for this node in TEST mode.
- MOCK mode returns predefined example data useful for demos and interface testing.
- Output CSV consolidates peptide scores for all inputs; expect multiple rows per protein corresponding to each sliding window peptide.

## Troubleshooting
- No output or empty CSV: Ensure at least one valid PDB input is provided and allele_name is correctly formatted.
- Unexpectedly long runtime: Reduce the number of input structures, choose TEST mode for a quick sanity check, or increase peptide window size cautiously to reduce the total number of windows.
- Invalid window_size error: Pick an integer within the allowed range (11–30).
- Allele not supported: Verify the allele_name string is correctly specified (e.g., DRB1*04:01) and supported by the underlying service.
- Mixed or duplicate protein names: Provide unique keys for each PDB in the input mapping to avoid overwriting results.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Functional Prediction/ImmunogenicityMHC2Node/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

