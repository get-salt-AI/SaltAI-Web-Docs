# PDB Chain Extractor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts specified chain(s) from one or more PDB structures and returns a new PDB containing only those chains. Preserves non-coordinate header information and appends an END record. Validates requested chain IDs and errors clearly if chains are not found.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbchainextractornode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to isolate specific chains from multichain PDB files for targeted modeling, analysis, or conversion. Connect a PDB dictionary input (e.g., from a loader or combiner) and provide a comma-separated list of chain IDs to keep. The output retains the input PDB keys, with each value reduced to the selected chains.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to filter by chain, formatted as {pdb_name: pdb_content}.</td><td style="word-wrap: break-word;">{"protein1": "ATOM ...\n...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to extract. Accepts formats like "A", "A,B", "A, B". Spaces are ignored and IDs are case-insensitive.</td><td style="word-wrap: break-word;">A,B</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures filtered to include only the requested chain(s), keyed by the original PDB names.</td><td style="word-wrap: break-word;">{"protein1": "HEADER...\nATOM ... (only chains A/B)\n...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain format**: Provide single-character, alphanumeric chain IDs separated by commas (e.g., "A,B"). Spaces are ignored and case is normalized.
- **Validation**: The node raises an error if the chains field is empty, contains invalid IDs, or none of the requested chains are present; it reports available chains when possible.
- **Output structure**: The output is a dictionary mirroring the input keys, with each value containing only the specified chains plus preserved header/meta lines and a final END record.
- **Case-insensitive**: Chain matching is case-insensitive; IDs are uppercased internally.
- **Content requirements**: Empty or whitespace-only PDB contents are skipped; if all are empty, the node errors.

## Troubleshooting
- **Error: Chains parameter cannot be empty**: Provide at least one chain ID, e.g., "A" or "A,B".
- **Error: Invalid chain ID '<id>'**: Ensure each chain ID is a single alphanumeric character (e.g., A, B, 1).
- **Error: No atoms found for chains ... Available chains: ...**: Check the chain labels present in your PDB and adjust the chains input accordingly.
- **Unexpected empty output for a specific entry**: Verify the corresponding PDB content is not empty and that requested chains exist in that structure.
- **Formatting issues in 'chains'**: If using spaces or mixed case (e.g., "a,  b"), this is supported; ensure chains are comma-separated and not semicolon-separated.
