# PDB Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges up to five PDB dictionaries into a single PDB output. Optionally prefixes chain IDs per input to avoid collisions and ensures unique chain IDs by auto-suffixing when duplicates occur.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/PDBCombinerNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to assemble a multi-chain PDB input from multiple PDB sources, such as combining separate structures or subunits into one complex. Connect one or more PDB inputs; enable chain prefixing to systematically keep chain IDs distinct across inputs.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB dictionary to merge. Expects a mapping of chain IDs to PDB content.</td><td style="word-wrap: break-word;">PDB with chains like A, B</td></tr>
<tr><td style="word-wrap: break-word;">pdb_2</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Second PDB dictionary to merge. Expects a mapping of chain IDs to PDB content.</td><td style="word-wrap: break-word;">PDB with chain C</td></tr>
<tr><td style="word-wrap: break-word;">pdb_3</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Third PDB dictionary to merge. Expects a mapping of chain IDs to PDB content.</td><td style="word-wrap: break-word;">PDB with chain A</td></tr>
<tr><td style="word-wrap: break-word;">pdb_4</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourth PDB dictionary to merge. Expects a mapping of chain IDs to PDB content.</td><td style="word-wrap: break-word;">PDB with chain D</td></tr>
<tr><td style="word-wrap: break-word;">pdb_5</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifth PDB dictionary to merge. Expects a mapping of chain IDs to PDB content.</td><td style="word-wrap: break-word;">PDB with chain E</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, adds per-input prefixes to chain IDs (PDB1_, PDB2_, etc.) to avoid chain ID collisions. Duplicate IDs are further resolved with numeric suffixes.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A single PDB dictionary containing all chains from the provided inputs with unique chain IDs.</td><td style="word-wrap: break-word;">PDB with chains like PDB1_A, PDB2_A, C</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input required**: The node raises an error if no PDB inputs are provided.
- **Chain ID uniqueness**: With prefix_chains enabled, chain IDs are prefixed per input and any remaining conflicts are auto-resolved by adding numeric suffixes (e.g., _1, _2).
- **Without prefixing**: If prefix_chains is false and a chain ID collision occurs, a warning is generated and numeric suffixes are still added to ensure uniqueness.
- **Input order matters for prefixes**: The prefix for each input is determined by its slot (PDB1_, PDB2_, ...).
- **Maximum inputs**: Supports up to five PDB inputs.

## Troubleshooting
- **Error: 'At least one PDB input is required'**: Connect at least one valid PDB input.
- **Unexpected chain renaming**: If you see suffixes like _1 or _2, it means a chain ID conflict occurred. Enable prefix_chains to systematically separate chain spaces by input.
- **Missing chains in output**: Ensure each provided PDB input is a valid PDB dictionary (mapping chain IDs to content) and not empty.
- **Confusing chain IDs after merge**: Turn on prefix_chains to make the source of each chain explicit (PDB1_, PDB2_, etc.).
