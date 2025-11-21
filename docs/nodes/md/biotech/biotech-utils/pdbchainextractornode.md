# PDB Chain Extractor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts specific chain(s) from one or more PDB structures and returns new PDBs containing only those chains. It preserves relevant header and metadata records and includes only ATOM/HETATM records for the requested chains, appending an END record.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbchainextractornode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to subset PDB structures by chain, for example to isolate a ligand-bound chain or to prepare inputs for downstream analysis that should include only selected chains. Provide a dictionary of PDBs (e.g., from a loader or combiner node) and a comma-separated list of chain IDs to keep.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to process, mapping identifiers to PDB text content (e.g., {"structure1": "...PDB text..."}). Each entry will be filtered to include only the specified chains.</td><td style="word-wrap: break-word;">{'1ABC': 'HEADER ...\\nATOM ... A ...\\nATOM ... B ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to extract. Accepts formats like "A", "A,B", "A, B", or "A,   B". Chain IDs must be single alphanumeric characters and are matched case-insensitively.</td><td style="word-wrap: break-word;">A,B</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of filtered PDB structures containing only the specified chains for each input entry. Keys mirror the input identifiers.</td><td style="word-wrap: break-word;">{'1ABC': 'HEADER ...\\nATOM ... A ...\\nATOM ... A ...\\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain ID validation**: Each chain ID must be a single alphanumeric character; invalid entries will cause an error.
- **Case-insensitive matching**: Chain IDs are matched case-insensitively (converted to uppercase internally).
- **Metadata retention**: Header and metadata records (e.g., HEADER, TITLE, REMARK, SEQRES, CRYST1) and MODEL lines are preserved; only ATOM/HETATM lines are filtered by chain.
- **END record**: An END record is appended to the output PDB content.
- **Strict per-entry validation**: If any input PDB contains none of the requested chains (no ATOM/HETATM lines match), the node raises an error and lists available chains in that PDB.
- **Empty inputs skipped**: PDB entries with empty content are skipped with a warning; if all are empty or invalid, the node errors.

## Troubleshooting
- **Chains parameter cannot be empty**: Provide at least one chain ID, e.g., "A" or "A,B".
- **Invalid chain ID '<id>'**: Ensure each chain ID is a single alphanumeric character (e.g., "A", "1").
- **No atoms found for chains [...]**: The specified chains do not exist in the PDB entry. Use the listed 'Available chains' from the error message and update the 'chains' input accordingly.
- **No valid PDB structures could be processed**: All inputs were empty or invalid. Verify the 'pdb' input dictionary contains non-empty PDB text.
- **Unexpected output structure**: The node returns a dictionary of filtered PDBs keyed by the original identifiers. Ensure downstream nodes expect a PDB dictionary.
- **Whitespace in chains**: Spaces are allowed and ignored around commas; ensure the list uses commas to separate chain IDs.
