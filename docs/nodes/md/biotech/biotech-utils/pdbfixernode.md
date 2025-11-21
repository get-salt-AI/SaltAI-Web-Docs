# PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Rewrites residue identities in a specified chain of one or more PDB structures to match a provided FASTA sequence. It parses the FASTA, maps 1-letter amino acids to 3-letter residue names, and updates the residue names for the target chain in-place, preserving coordinates and atom records. If sequence length differs from the chain residue count, it replaces as many residues as possible and warns about the mismatch.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbfixernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after loading PDB files and a target FASTA sequence when you need a PDB chain's residue identities to match a designed or target sequence (e.g., workflows involving structure generation then sequence design). Typical flow: Load PDB -> Load/prepare FASTA -> PDB Fixer (set target_chain) -> downstream design/refinement or export.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to modify, keyed by name. Each value is the full PDB file content as a string.</td><td style="word-wrap: break-word;">{'1abc_fixed': 'ATOM      1  N   ALA A   1 ...', 'design_model': 'ATOM      1  N   GLY B   1 ...'}</td></tr>
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA content containing the target amino acid sequence (1-letter codes). Header lines starting with '>' are supported; the sequence can span multiple lines.</td><td style="word-wrap: break-word;">>target_chain ACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">target_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain identifier in the PDB to rewrite (e.g., "A" or "B").</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures with the specified chain's residue names updated to match the FASTA sequence. Keys mirror the input names.</td><td style="word-wrap: break-word;">{'design_model': 'ATOM      1  N   ALA B   1 ...\nATOM      2  CA  ALA B   1 ...\n...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Residue identity only**: This node updates residue names (3-letter codes) for atoms in the target chain; it does not change coordinates, atom counts, backbone, or numbering.
- **Sequence length mismatch**: If the FASTA length differs from the number of residues detected in the chain, only the overlapping positions are replaced. A warning is issued.
- **Chain detection**: The chain must exist in ATOM/HETATM records with a standard chain ID character at column 22 (index 21). If not found, processing fails for that PDB.
- **Unknown residues**: Any 1-letter code not in the supported set is left unchanged at that position and triggers a warning.
- **Multiple inputs supported**: Accepts a dictionary of multiple PDBs and processes each independently.
- **Standard residues expected**: Works best when the chain is composed of standard amino acids; non-protein residues are skipped for identity inference.
- **Case handling**: FASTA amino acids are case-insensitive; they are normalized to uppercase for mapping.

## Troubleshooting
- **Error: 'PDB input must be a non-empty dictionary'**: Ensure you pass a dictionary mapping names to PDB file contents and that strings are non-empty.
- **Error: 'Target chain ID cannot be empty' or 'Chain <X> not found in PDB'**: Confirm the chain ID exists in the PDB (check the chain column in ATOM records) and matches exactly.
- **Error: 'FASTA content cannot be empty' or 'No sequence found in FASTA data'**: Provide valid FASTA with at least one non-header sequence line.
- **Sequence length mismatch warnings**: This is expected if the target chain length differs from the FASTA. Verify you chose the correct chain or adjust the sequence.
- **Unexpected amino acid warnings (e.g., 'Unknown amino acid X')**: Replace unsupported characters in the FASTA (e.g., X, B, Z) with standard 1-letter amino acid codes.
- **No changes observed**: Verify that the specified chain matches the chain in the PDB, the FASTA length overlaps the residue count, and the sequence uses standard amino acid letters.
