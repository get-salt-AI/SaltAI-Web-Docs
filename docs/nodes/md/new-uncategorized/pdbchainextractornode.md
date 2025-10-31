# PDB Chain Extractor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts specific chain(s) from one or more PDB structures. Given a dictionary of PDB contents and a comma-separated list of chain IDs, it filters the ATOM/HETATM records to only those chains while preserving relevant header and annotation lines. Returns a new PDB dictionary with the same keys, containing only the requested chains.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/pdbchainextractornode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to isolate particular chains from complex or multimeric PDB structures before downstream steps such as sequence extraction, format conversion, design, or visualization. Connect a PDB dictionary input and specify chain IDs (e.g., A or A,B) to produce a filtered PDB suitable for per-chain analyses.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to filter, keyed by PDB ID or name. Each value is a full PDB text string.</td><td style="word-wrap: break-word;">{'1ABC': 'HEADER ...\\nATOM ... A ...\\nATOM ... B ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to extract. Spaces are ignored and matching is case-insensitive. Each chain ID must be a single alphanumeric character.</td><td style="word-wrap: break-word;">A, B</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of filtered PDB structures with only the specified chains kept for each input entry.</td><td style="word-wrap: break-word;">{'1ABC': 'HEADER ...\\nATOM ... A ...\\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain ID format**: Each chain ID must be a single alphanumeric character; input is case-insensitive and whitespace around commas is ignored.
- **Batch behavior**: Processes all entries in the input PDB dictionary and returns a dictionary with the same keys, filtered by the specified chains.
- **Preserved records**: Retains common header/annotation lines (e.g., HEADER, TITLE, HELIX, SHEET, CRYST1) and MODEL records, but only includes ATOM/HETATM lines for the requested chains.
- **Validation**: Raises an error if no valid chain IDs are provided or if none of the specified chains are found; the error lists available chains detected in the input.
- **Empty inputs**: Skips empty PDB contents with a warning; if all entries are empty or invalid, processing fails.

## Troubleshooting
- **No chains provided**: If you see an error that chains cannot be empty, provide at least one chain ID like 'A' or 'A,B'.
- **Invalid chain ID**: If an error mentions invalid chain ID format, ensure each chain ID is a single alphanumeric character (e.g., 'A', '1').
- **Chains not found**: If it reports no atoms found for requested chains, verify the chain IDs exist in the source PDB; the error message lists available chains you can use.
- **Empty PDB content**: If entries are skipped as empty, confirm upstream nodes are producing PDB strings and connections are correct.
- **Unexpected output size**: If output is missing chains, confirm your 'chains' string has commas between IDs and no accidental characters; spaces are allowed and ignored.
