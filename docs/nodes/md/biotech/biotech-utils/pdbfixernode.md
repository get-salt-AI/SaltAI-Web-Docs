# PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Replaces the residue identities of a specific chain in one or more PDB structures using a provided FASTA sequence. Coordinates, atom counts, and residue numbering are preserved; only the residue three-letter codes in the target chain are changed. If the FASTA length differs from the chain length, it replaces as many residues as possible and leaves the rest unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbfixernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to retarget a designed backbone (e.g., a glycine-only chain from backbone generation) with a desired amino-acid sequence for downstream design or scoring workflows. Typical flow: load or create PDB(s) → prepare a FASTA sequence for the replacement chain → run PDB Fixer with the target chain ID → pass the fixed PDB(s) to subsequent nodes.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to modify: {pdb_name: pdb_content}. The node will update only the specified chain in each entry.</td><td style="word-wrap: break-word;">{'design_1': 'ATOM ...\nATOM ...\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA text containing the replacement amino-acid sequence. Headers (lines starting with '>') are allowed and ignored; the final sequence is taken as the concatenation of non-header lines.</td><td style="word-wrap: break-word;">>target_chain ACDEFGHIKLMNPQRSTVWY</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of updated PDB structures with the target chain’s residues replaced according to the FASTA sequence. Other chains are unchanged.</td><td style="word-wrap: break-word;">{'design_1': 'ATOM ... (with updated residue names in chain B) ...\nEND\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Residue mapping**: The FASTA sequence (1-letter codes) is mapped to 3-letter residue names (e.g., A→ALA, G→GLY). Unknown letters are skipped and original residue names are kept.
- **Length mismatches**: If FASTA length differs from the number of residues in the chain, only the overlapping positions are replaced; extra residues on either side remain unchanged.
- **Scope of change**: Only the residue names in ATOM/HETATM records of the target chain are modified. Coordinates, atom order, residue numbering, and other chains are preserved.
- **Chain requirement**: The specified chain ID must exist in each PDB. If a chain is missing, processing for that PDB fails.
- **FASTA parsing**: Headers are ignored and all non-header lines are concatenated (whitespace removed) to form the sequence.
- **Allowed codes**: Supports standard amino acids (including SEC 'U' and PYL 'O'). 'X' maps to UNK; any unrecognized character leaves the original residue unchanged.
- **Input format**: The PDB input must be a non-empty dictionary: {pdb_name: pdb_content}.

## Troubleshooting
- **Error: 'PDB input must be a non-empty dictionary'**: Ensure you pass a dictionary with keys as names and values as PDB strings.
- **Error: 'Target chain ID cannot be empty'**: Provide a single-character chain ID (e.g., 'A').
- **Error: 'Chain 'X' not found in PDB'**: Confirm the chain exists in the PDB. Check chain IDs by inspecting ATOM/HETATM records (column 22).
- **FASTA parsed as empty**: Verify the FASTA contains sequence lines beneath any header and that there are no non-amino characters.
- **Sequence length mismatch warning**: This is expected if the FASTA and chain lengths differ. The node will replace only the overlapping residues.
- **Unexpected characters in FASTA**: Non-standard letters are ignored for replacement at those positions; the original residue is kept.
- **Malformed PDB lines**: If the file lacks standard ATOM/HETATM formatting, replacement may fail. Use a validated PDB or convert/clean the structure before running.
