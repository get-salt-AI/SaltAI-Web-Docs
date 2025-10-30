# PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Replaces the residue types of a specific chain in one or more PDB structures with a provided FASTA sequence. The node keeps atom coordinates and residue numbering, modifying only the 3-letter residue names to match the FASTA sequence. If the FASTA length differs from the chain length, it replaces as many residues as possible and logs a warning.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/PdbFixerNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to substitute a placeholder chain (for example, a poly-Gly chain produced by design tools) with a target amino acid sequence while preserving the original PDB geometry. Typical workflow: load or generate a PDB, provide a FASTA sequence for the desired chain, set the target chain ID (e.g., 'B'), and feed the fixed PDB into downstream modeling or design nodes.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">PDB structure(s) to modify. Expects a dictionary mapping PDB IDs to PDB text content.</td><td style="word-wrap: break-word;">{'template_1': 'ATOM      1  N   GLY A   1 ...', 'template_2': 'ATOM      1  N   GLY B   1 ...'}</td></tr>
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA sequence to apply to the target chain. Headers are ignored; only sequence lines are used. Whitespace is stripped.</td><td style="word-wrap: break-word;">>target_chain_B MKTFFVAGL</td></tr>
<tr><td style="word-wrap: break-word;">target_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Single-character chain ID to replace (e.g., 'A', 'B').</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures with the target chain's residue names replaced according to the FASTA sequence while retaining coordinates and numbering.</td><td style="word-wrap: break-word;">{'template_1': 'ATOM      1  N   MET B   1 ...', 'template_2': 'ATOM      1  N   LYS B   1 ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The PDB input must be a non-empty dictionary {pdb_name: pdb_string}.
- **Target chain**: Must be a single alphanumeric character matching the chain ID present in the PDB (position 22 in standard PDB lines).
- **FASTA parsing**: Headers (lines starting with '>') are ignored; sequence lines are concatenated with whitespace removed.
- **Residue mapping**: The node maps 1-letter amino acids to 3-letter codes (A→ALA, R→ARG, ..., U→SEC, O→PYL, X→UNK). Unknown letters are left as original residue names.
- **Length mismatch**: If the FASTA length differs from the number of residues detected in the target chain, it replaces residues up to the shorter length and logs a warning.
- **Scope of changes**: Only residue names are changed for ATOM/HETATM records of the target chain; coordinates, atom names, residue numbering, and all other records remain unchanged.
- **Per-residue logic**: Replacement is based on residue sequence order inferred from residue number changes (unique residues by number). Multiple atoms for the same residue are updated consistently.
- **Multiple PDBs**: Supports batch operation; each PDB entry in the input dictionary is processed independently.
- **Requirements**: PDB lines must follow standard fixed-column formatting for chain ID (col 22) and residue number (cols 23–26).
- **Failure cases**: The node raises an error if the PDB dict is empty/invalid, the chain is not found, or the FASTA contains no sequence.

## Troubleshooting
- **Chain not found**: Error like "Chain 'B' not found" indicates the target_chain does not exist in the input PDB. Verify the chain ID and that your PDB uses standard chain identifiers.
- **Empty or invalid FASTA**: If you see "FASTA content cannot be empty" or "No sequence found in FASTA data", ensure your FASTA includes sequence lines beneath any header.
- **Length mismatch warning**: If you get a warning about different lengths, confirm your FASTA matches the chain length. The node will still replace up to the shorter length.
- **Unknown amino acids**: If warnings appear about unknown letters, replace nonstandard characters in the FASTA or note that those residues will keep original 3-letter names.
- **Unexpected output structure**: If the output is not usable downstream, ensure the input was a dict of {id: pdb_content} and that your PDB lines maintain correct column formatting.
