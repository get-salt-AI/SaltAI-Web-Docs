# PDB Chain Extractor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts one or more specified chains from input PDB structures and returns a new PDB containing only those chains. Chain selection is case-insensitive, whitespace-tolerant, and validated to single alphanumeric IDs. Non-coordinate records such as headers and secondary structure annotations are preserved; only ATOM/HETATM for the selected chains are retained.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbchainextractornode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to isolate specific chains from multi-chain PDB structures before downstream analysis, visualization, conversion, or modeling. Typical workflow: load or batch PDBs → PDB Chain Extractor (specify chain IDs like A,B) → pass filtered PDBs to visualization, conversion (e.g., CIF), or modeling nodes.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Input PDB structure(s) as a dictionary mapping {pdb_id: pdb_content}. Each value should be a valid PDB-formatted string.</td><td style="word-wrap: break-word;">{"1ABC": "HEADER ...\nATOM ...\nHETATM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to extract. Accepts formats like "A", "A,B", "A, B", or with extra spaces; spaces are ignored. Chain IDs are treated case-insensitively and must be single alphanumeric characters.</td><td style="word-wrap: break-word;">A,B</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary {pdb_id: pdb_content} where each PDB contains only the specified chains. Preserves header/annotation records and includes ATOM/HETATM only for selected chains.</td><td style="word-wrap: break-word;">{"1ABC": "HEADER ...\nATOM ... (only chains A/B)\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Chain IDs must be single alphanumeric characters; invalid IDs cause an error.
- The chains parameter cannot be empty; at least one valid chain must be provided.
- Selection is case-insensitive and ignores spaces around commas.
- If a requested chain is not found, the node raises an error listing available chains discovered in the input.
- Only ATOM/HETATM records for the specified chains are kept; many header and annotation records (e.g., HEADER, TITLE, HELIX, SHEET) are preserved; END is appended.
- Input must be a PDB dictionary {pdb_id: pdb_string}. Empty PDB content entries are skipped with a warning.
- If no valid PDBs remain after processing, the node raises an error.

## Troubleshooting
- No atoms found for chains: Ensure the requested chain IDs exist in the input PDB. The error message lists available chains detected; adjust the 'chains' input accordingly.
- Invalid chain ID: Chain IDs must be single alphanumeric characters (e.g., A, B, 1). Remove multi-character or special-character IDs.
- Empty 'chains' input: Provide at least one chain (e.g., A or A,B). Spaces are allowed around commas.
- Output is empty or node errors with 'No valid PDB structures could be processed': Verify that the input PDB dictionary contains non-empty, valid PDB strings.
- Unexpected case or whitespace issues: The node ignores spaces and is case-insensitive, but ensure commas separate chain IDs (e.g., A, B, not A;B).
