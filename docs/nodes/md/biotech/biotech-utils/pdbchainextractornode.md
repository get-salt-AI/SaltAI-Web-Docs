# PDB Chain Extractor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes one or more PDB structures (stored in a dictionary) and filters them down to a subset of chains based on user-provided chain IDs. It scans ATOM and HETATM records, keeps only lines belonging to the selected chains, preserves common header and annotation records, and appends a final END record for a valid PDB output. The node validates chain IDs, reports when requested chains are missing (listing available chains), and returns a filtered PDB dictionary suitable for subsequent biotech nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbchainextractornode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to isolate specific chains from one or multiple PDB structures, such as extracting a single monomer from a multimeric complex or focusing on specific chains for design, refinement, or analysis. It is typically used downstream of PDB-producing nodes like "LoadPDBNode", "BatchPDBNode", or "PDBCombinerNode", which output dictionaries of the form {pdb_id: pdb_content}. In a typical workflow, you might: (1) load a structure with "LoadPDBNode", (2) pass its output to "PDBChainExtractorNode" and specify chains like A or A,B, and then (3) send the filtered output into nodes such as "PDBToFastaNode" (to extract sequences only for those chains), "PdbFixerNode" (to replace sequences on selected chains), "PDBVisualizationNode" (to visualize only the chains of interest), or exporting nodes like "SaveToZipNode" and "SaveToBucketNode". The chains parameter accepts flexible comma-separated input (for example A, A,B, A, B, or A,   B), and the node enforces that each chain ID is a single alphanumeric character to match typical PDB chain naming.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A PDB-typed value containing a dictionary mapping PDB IDs to PDB content strings: {pdb_id: pdb_text}. Each value should be a valid PDB-formatted string with ATOM and/or HETATM records and standard header/annotation lines. Empty content or non-PDB text will be ignored for extraction and may lead to errors if no valid structures remain.</td><td style="word-wrap: break-word;">{   "1ABC": "HEADER    EXAMPLE PDB\nATOM      1  N   MET A   1      ...\nATOM      2  CA  MET A   1      ...\nATOM      3  N   GLY B   1      ...\nEND\n" }</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to extract. The string is split on commas and surrounding whitespace is removed; empty segments are discarded. Each chain ID must be a single alphanumeric character (for example A, B, 1, or X). At least one valid chain ID must be provided, otherwise an error is raised.</td><td style="word-wrap: break-word;">A,B</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A PDB-typed dictionary mapping the original PDB IDs to new PDB content strings that contain only the requested chains. Header and annotation records such as HEADER, TITLE, REMARK, SEQRES, HELIX, and SHEET are preserved where present, while only ATOM and HETATM records with chain IDs in the requested set are kept. An END line is appended at the end of each filtered structure. This output can be used directly by downstream nodes expecting PDB dictionaries, including "PDBToFastaNode", "PdbFixerNode", "PDBVisualizationNode", "SaveToZipNode", and "SaveToBucketNode".</td><td style="word-wrap: break-word;">{   "1ABC": "HEADER    EXAMPLE PDB\nATOM      1  N   MET A   1      ...\nATOM      2  CA  MET A   1      ...\nEND\n" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node operates via line-based string filtering and is generally fast, but total runtime grows with the combined size and number of input PDBs; very large batches of large structures will take proportionally longer.
- **Limitations**: Only ATOM and HETATM records are inspected when selecting chains. If the requested chains have no ATOM or HETATM entries (for example only annotations), they will be treated as absent, and an error will be raised for that PDB.
- **Behavior**: If a PDB does not contain any atoms for the requested chains, the node raises a ValueError listing the requested chains and the set of chains actually present in that structure instead of returning an empty result.
- **Behavior**: Chain IDs are validated to be exactly one alphanumeric character; multi-character IDs or symbols are rejected and cause an explicit error. This matches the expected PDB chain column semantics.
- **Behavior**: The output dictionary is passed through SaltAI’s metadata wrapper so that downstream save and export nodes can infer appropriate folder and file naming when writing results.

## Troubleshooting
- **Error about invalid chain ID**: If you see a message like "Invalid chain ID 'XY'. Chain IDs should be single alphanumeric characters.", the chains input contains an entry longer than one character or containing non-alphanumeric symbols. Fix this by providing single-character IDs only, such as changing XY to X or Y.
- **Error that no atoms were found for chains**: An error like "No atoms found for chains ['A', 'B'] in PDB '1ABC'. Available chains: C, D" means that none of the requested chains are present in the structure. Check which chains actually exist in the PDB (as listed in the message) and adjust the chains input, for example to C,D.
- **Error that chains parameter cannot be empty**: If the chains field is left blank or only contains spaces, the node raises an error requiring at least one chain ID. Enter at least one chain, such as A.
- **Error that no valid PDB structures could be processed**: This indicates that all PDB entries were either empty, invalid, or failed chain extraction (for example due to missing requested chains). Verify that the upstream node is providing a well-formed PDB dictionary and that the chains you request actually exist in those structures.
