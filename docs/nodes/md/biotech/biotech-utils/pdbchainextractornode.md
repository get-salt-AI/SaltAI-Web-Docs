# PDB Chain Extractor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts one or more specified chains from PDB structure(s) and returns filtered PDB content containing only those chains. Accepts a dictionary of PDB entries and validates chain IDs, preserving standard header/annotation records while including only ATOM/HETATM records for the requested chains.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbchainextractornode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to isolate specific chains from multi-chain PDB structures before analysis, visualization, or downstream modeling. Typical workflow: load or combine PDBs → PDB Chain Extractor (specify chains like A,B) → pass filtered structures to subsequent nodes (e.g., conversion to CIF, FASTA extraction, or saving).

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to process, mapping structure identifiers to their PDB content strings.</td><td style="word-wrap: break-word;">{"4HHB": "<pdb-file-content>", "my_structure": "<pdb-file-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to extract. Spaces are allowed and ignored. Chain IDs must be single alphanumeric characters.</td><td style="word-wrap: break-word;">A,B</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of filtered PDB structures containing only the specified chains for each input entry.</td><td style="word-wrap: break-word;">{"4HHB": "<filtered-pdb-content>", "my_structure": "<filtered-pdb-content>"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Chain IDs must be single alphanumeric characters (case-insensitive) and provided as a comma-separated list, e.g., "A,B" or "A, B".
- If no valid chain IDs are supplied or the list is empty, the node raises an error.
- If none of the specified chains are found in an input PDB, the node fails with an error listing available chains in that structure.
- Headers and annotation records are preserved; only ATOM/HETATM records are filtered to the specified chains.
- Input must be a non-empty dictionary mapping names to PDB content strings.

## Troubleshooting
- Error: "Chains parameter cannot be empty" — Provide at least one chain ID (e.g., A).
- Error: "Invalid chain ID 'X'" — Ensure each chain ID is a single alphanumeric character; remove multi-character or special-symbol entries.
- Error: "No atoms found for chains [...]" — Verify that the requested chains exist in the input PDB; check the error message for the list of available chains and adjust the chain list accordingly.
- Issue: Empty or blank PDB content — Ensure each dictionary value contains valid PDB text; remove or replace empty entries.
- Unexpected output content — Confirm the chain list formatting (use commas) and that whitespace around chain IDs is acceptable (spaces are ignored).
