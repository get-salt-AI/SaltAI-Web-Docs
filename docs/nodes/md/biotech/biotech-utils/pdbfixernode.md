# PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Replaces the amino-acid identities of a specified chain in one or more PDB structures to match a provided FASTA sequence. It updates residue names (1-letter to 3-letter codes) for residues in the target chain without adding/removing residues or altering atom coordinates. If the FASTA length differs from the chain length, it replaces as many residues as possible and leaves the rest unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbfixernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to align a PDB chain's residue identities to a desired sequence, for example after backbone generation and before sequence design or scoring (e.g., RFdiffusion + ProteinMPNN workflows). Provide a dictionary of PDB contents, the FASTA sequence to apply, and the target chain ID. The node outputs updated PDB content with the chain's residue names adjusted to the provided sequence.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structure(s) to modify, keyed by name. Each value is the full PDB text content.</td><td style="word-wrap: break-word;">{'design_001.pdb': 'ATOM      1  N   GLY B   1 ...'}</td></tr>
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA content containing the target sequence that should replace the specified chain's sequence. Header lines (starting with '>') are allowed; only sequence lines are used.</td><td style="word-wrap: break-word;">>target_sequence GASSTVWYRKLMNQDEPHC </td></tr>
<tr><td style="word-wrap: break-word;">target_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID to update (single-character typical, e.g., 'A', 'B'). The sequence from the FASTA is mapped positionally to residues found in this chain.</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of updated PDB structure(s) keyed by the original names, with residue names in the target chain replaced to match the FASTA sequence.</td><td style="word-wrap: break-word;">{'design_001.pdb': 'ATOM      1  N   ALA B   1 ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only residue identities (3-letter names) are changed; no atoms, coordinates, residue numbering, or insertions/deletions are performed.
- If the FASTA sequence length differs from the number of residues detected in the target chain, the node replaces residues up to the shorter length and logs a warning.
- Supported amino acids include the standard set plus SEC (U), PYL (O), and UNK (X). Unknown letters are ignored for replacement at their positions, keeping the original residue.
- The target chain must exist in the PDB; otherwise the process fails for that PDB.
- Both ATOM and HETATM records for residues of the target chain are updated where applicable.
- Input 'pdb' must be a non-empty dictionary of {name: pdb_content}. Empty entries are skipped.
- FASTA parsing ignores header lines ('>...') and concatenates sequence lines; whitespace is removed.

## Troubleshooting
- Chain not found: Ensure the 'target_chain' ID exists in the provided PDB(s), and that the PDB content includes standard ATOM/HETATM lines for that chain.
- Empty or invalid FASTA: Provide a valid FASTA with at least one sequence line; headers are allowed but sequence cannot be empty.
- Length mismatch results: If only part of the chain was updated, verify that your FASTA length matches the number of residues in the target chain, or adjust expectations for partial replacement.
- Unknown amino acids reported: Replace non-standard or unsupported letters in the FASTA with supported codes (A, R, N, D, C, Q, E, G, H, I, L, K, M, F, P, S, T, W, Y, V, U, O, X).
- No output produced: Confirm 'pdb' is a non-empty dictionary and each value contains valid PDB text; empty entries are skipped and may lead to no processed outputs if all are empty.
