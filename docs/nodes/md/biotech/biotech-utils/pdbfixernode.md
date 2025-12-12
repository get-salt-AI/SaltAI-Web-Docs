# PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Replaces the residue types of a specified chain in PDB structure(s) with a provided FASTA sequence. It maps 1-letter amino acids to 3-letter residue names and updates all atoms belonging to the target chain. If the FASTA and chain lengths differ, it replaces as many residues as possible and leaves the rest unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbfixernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this when you need to retarget a designed or placeholder chain in a PDB to a specific sequence, such as replacing an RFdiffusion GLY chain before running downstream design tools (e.g., ProteinMPNN). Provide one or multiple PDBs as a name-to-content dictionary, a FASTA sequence, and the chain ID to modify.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more PDB structures to modify, as a dictionary mapping {pdb_name: pdb_content}.</td><td style="word-wrap: break-word;">{'my_structure.pdb': 'ATOM      1  N   GLY A   1 ...\\nATOM      2  CA  GLY A   1 ...\\n...'}</td></tr>
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">A FASTA string whose sequence will replace the residue types of the target chain. Header lines are allowed and ignored.</td><td style="word-wrap: break-word;">>target_chain\nACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">target_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The chain ID to modify (single-letter ID, e.g., B).</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Modified PDB structure(s) with the specified chain’s residue names replaced according to the FASTA sequence. Returned as a dictionary {pdb_name: pdb_content}.</td><td style="word-wrap: break-word;">{'my_structure.pdb': 'ATOM      1  N   ALA B   1 ...\\nATOM      2  CA  ALA B   1 ...\\n...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Sequence mapping**: Uses standard 1-letter to 3-letter amino acid mapping (e.g., A -> ALA). Unknown letters are left unchanged.
- **Length mismatch**: If FASTA length differs from the chain’s residue count, only the overlapping portion is replaced; no residues are added or removed.
- **Scope of changes**: Only residue names are updated; atom positions, residue numbering, and chain length remain unchanged.
- **Targeting**: The change applies to all ATOM/HETATM records for the specified chain ID.
- **Multiple inputs**: Accepts multiple PDBs at once via a name-to-content dictionary.

## Troubleshooting
- **Error: 'PDB input must be a non-empty dictionary'**: Ensure you pass a dictionary like {"file.pdb": "...pdb text..."} and not an empty value.
- **Error: 'Target chain ID cannot be empty'**: Provide a single-letter chain ID (e.g., A, B).
- **Error: 'Chain <X> not found in PDB'**: Verify the chain ID exists in the PDB (check column 22 of PDB format).
- **Error: 'FASTA content cannot be empty' or 'No sequence found in FASTA data'**: Include a valid FASTA with at least one sequence line; headers starting with '>' are allowed.
- **Unexpected residues remain unchanged**: If the FASTA contains unknown amino acid codes, those positions are kept as the original residue names.
- **Length mismatch warning**: If you see a warning about different lengths, confirm your target chain and sequence match. Only the first N residues (min(PDB_chain_len, FASTA_len)) are replaced.
