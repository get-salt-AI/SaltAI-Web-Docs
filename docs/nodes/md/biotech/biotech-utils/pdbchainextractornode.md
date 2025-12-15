# PDB Chain Extractor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts specified chains from one or more PDB structures. It retains relevant header/metadata lines and all ATOM/HETATM records for the selected chains, producing a new PDB that contains only those chains. Chain selection is case-insensitive and whitespace-tolerant.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbchainextractornode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to isolate specific protein chains from PDB structures for focused analysis or downstream tasks (e.g., per-chain sequence extraction, modeling, or visualization). Connect a PDB dictionary input and specify one or more chain IDs (e.g., A, A,B). The output preserves the original PDB IDs while filtering contents to the requested chains.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to filter, mapping {pdb_id: pdb_content}. Each value is a PDB text string.</td><td style="word-wrap: break-word;">{"1abc": "HEADER ...\nATOM ... A ...\nATOM ... B ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to extract. Single-character, alphanumeric chain IDs only. Spaces are ignored and case-insensitive.</td><td style="word-wrap: break-word;">A, B</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures filtered to contain only the specified chains. Keys are the original PDB IDs; values are PDB strings that include the chosen chains and end with an END record.</td><td style="word-wrap: break-word;">{"1abc": "HEADER ...\nATOM ... A ...\nATOM ... A ...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain IDs**: Must be single alphanumeric characters (e.g., A, B, 1). Input is case-insensitive and ignores spaces.
- **Multiple PDBs**: Input may contain a batch of PDBs; each will be filtered independently while preserving its original ID.
- **Metadata lines**: Common header and annotation records (e.g., HEADER, TITLE, REMARK, HELIX, SHEET) are preserved. ATOM/HETATM lines are kept only for selected chains.
- **End of file**: The output PDB string includes a terminal END line.
- **Validation**: If no atoms are found for the requested chains in a PDB, the node raises an error listing available chains detected in that PDB.
- **Empty entries**: Empty PDB contents are skipped; if all are empty or invalid, the node raises an error.

## Troubleshooting
- **Error: Chains parameter cannot be empty**: Provide at least one chain ID, e.g., "A" or "A,B".
- **Error: Invalid chain ID 'X'**: Ensure each chain ID is a single alphanumeric character with no extra symbols.
- **Error: No atoms found for chains [...]**: Verify the requested chains exist in the PDB. The error message includes available chains detected; adjust your selection accordingly.
- **Output is empty or missing expected chains**: Confirm the chain list formatting (comma-separated), check for typos, and ensure the original PDB actually contains those chains.
- **Batch input issues**: If some PDBs are empty or malformed, they will be skipped. Ensure at least one valid PDB remains; otherwise, the node will raise an error.
