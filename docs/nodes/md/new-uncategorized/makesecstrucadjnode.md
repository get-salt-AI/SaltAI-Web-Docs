# Make Secstruc Adj

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates secondary structure (_ss.pt) and block adjacency (_adj.pt) tensors from provided PDB structures. It prepares scaffold conditioning artifacts required for fold-guided protein design workflows. The node validates input PDBs and returns a portable reference to the generated .pt files.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/makesecstrucadjnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after assembling or loading one or more PDB structures. Connect the PDB output (e.g., from a PDB Combiner or PDB Loader) into this node to produce the scaffold .pt files. Then feed the returned scaffold_pt_files into downstream scaffold-guided configuration or generation nodes.

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
<tr><td style="word-wrap: break-word;">pdbs</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more PDB structures to process. Expects a mapping of names to PDB text content, typically provided by earlier nodes that load or combine PDBs.</td><td style="word-wrap: break-word;">{'scaffold_A': 'ATOM ... END', 'scaffold_B': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to allow for generating the .pt files. Increase for larger or multiple PDBs.</td><td style="word-wrap: break-word;">300</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Reference to the generated secondary structure and adjacency tensors for each input scaffold. Suitable for direct connection into scaffold-guided/fold conditioning nodes.</td><td style="word-wrap: break-word;">{'type': 'inline', 'pt_files': {'scaffold_A': {'ss': '<bytes-or-path-to-ss-pt>', 'adj': '<bytes-or-path-to-adj-pt>'}, 'scaffold_B': {'ss': '<bytes-or-path-to-ss-pt>', 'adj': '<bytes-or-path-to-adj-pt>'}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- At least one valid PDB structure is required; the input must be a mapping of names to PDB contents.
- The node produces two artifacts per scaffold: *_ss.pt (secondary structure) and *_adj.pt (block adjacency).
- Timeout is configurable between 60 and 1800 seconds; larger or more complex inputs may require higher values.
- Output is returned as a reference object that downstream nodes can consume directly for fold conditioning.
- Ensure upstream nodes provide correctly formatted PDB content; malformed or empty inputs will cause the operation to fail.

## Troubleshooting
- No .pt files were generated: Verify that each provided PDB is valid and complete; try with a single known-good PDB to isolate issues.
- Operation timed out: Increase the timeout input value (e.g., to 600 or higher) for large or multiple PDBs.
- Invalid input type error: Confirm the 'pdbs' input is a dictionary-like mapping from scaffold names to PDB text.
- Downstream node rejects output: Ensure you connect the 'scaffold_pt_files' output directly into nodes expecting scaffold conditioning files.
