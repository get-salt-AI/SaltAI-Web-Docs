# PDB Chain Extractor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts specified chains from one or more PDB structures. It preserves key header/metadata lines and all ATOM/HETATM records for the selected chains, producing new PDB contents that contain only those chains. Chain selection is case-insensitive and tolerant to whitespace in the chain list.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbchainextractornode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to isolate specific protein chains from PDB structures for focused analysis or downstream tasks such as per-chain sequence extraction, modeling, scoring, or visualization. Provide a PDB dictionary input (mapping PDB IDs to PDB text) and specify one or more target chain IDs (e.g., "A" or "A,B"). The node returns a new dictionary with the same PDB IDs but with contents filtered to only the requested chains.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to filter, mapping {pdb_id: pdb_content}. Each value must be a full PDB text string including header, ATOM, and HETATM records.</td><td style="word-wrap: break-word;">{"1abc": "HEADER EXAMPLE STRUCTURE\nATOM      1  N   MET A   1 ...\nATOM      2  CA  MET B   1 ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to extract. Each chain ID must be a single alphanumeric character (e.g., A, B, 1). Input is case-insensitive; spaces around commas are ignored.</td><td style="word-wrap: break-word;">A, B</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures filtered to contain only the specified chains. Keys are the original PDB IDs; values are PDB strings that retain relevant metadata and only the selected chains' atoms, each ending with an END record.</td><td style="word-wrap: break-word;">{"1abc": "HEADER EXAMPLE STRUCTURE\nATOM      1  N   MET A   1 ...\nATOM      2  CA  MET A   1 ...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain IDs**: Each chain identifier must be a single alphanumeric character (e.g., A, B, 1). The parameter is case-insensitive and ignores whitespace around commas in the list.
- **Multiple PDBs**: The input can contain a batch of PDBs; each entry is filtered independently while preserving its original dictionary key (the PDB ID).
- **Metadata preservation**: Common header and annotation records (such as HEADER, TITLE, REMARK, HELIX, and SHEET) are preserved in the filtered output, while ATOM/HETATM lines are retained only for the chosen chains.
- **End record**: Each output PDB string includes a terminal END line so the results are well-formed PDB files.
- **Validation behavior**: If no atoms are found for the requested chains in a particular PDB, the node raises an error and reports which chains were actually detected in that structure.
- **Empty or invalid entries**: Empty or malformed PDB contents are skipped; if all entries are empty or invalid, the node raises an error rather than returning an empty dictionary.

## Troubleshooting
- **Error: Chains parameter cannot be empty**: Provide at least one chain ID, such as "A" or "A,B". Ensure the chains field is not blank.
- **Error: Invalid chain ID 'X'**: Check that every chain ID is a single alphanumeric character without extra symbols or multiple-character labels. Remove internal spaces and use commas solely as separators.
- **Error: No atoms found for chains [...]**: The requested chains do not exist in that PDB. Inspect the error message, which lists available chains detected in the file, and adjust your chain list accordingly.
- **Output is empty or missing expected chains**: Verify the chain list formatting (comma-separated), check for typos, ensure there are no hidden characters, and confirm that the original PDB actually contains those chains.
- **Batch input issues (some PDBs missing from output)**: If certain PDBs are absent in the results, they may have been empty or malformed and thus skipped. Validate those specific inputs and ensure at least one valid PDB is present so the node can complete successfully.
