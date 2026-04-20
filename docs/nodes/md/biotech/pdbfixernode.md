# PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node adjusts one chain in one or more PDB structures so that the residue identities match a provided FASTA sequence. It parses the FASTA, maps 1-letter amino acids to 3-letter PDB residue codes, and overwrites residue names for the target chain while preserving coordinates, chain IDs, and residue numbering. It is tailored to workflows where a generic scaffold chain is converted into a designed sequence before further modeling or design steps.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/biotech/pdbfixernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a PDB scaffold and a designed amino acid sequence that should replace the residue types of a specific chain, while keeping the backbone geometry unchanged. A typical scenario is RFdiffusion plus ProteinMPNN workflows, where RFdiffusion outputs a glycine-only chain and ProteinMPNN returns a designed sequence that must be embedded into the PDB for downstream tools.

In a common pipeline, you obtain structures from nodes such as Load PDB, Batch PDB, or PDB Combiner and feed them to the pdb input as a dictionary from pdb_name to pdb_content. The target sequence usually comes from a sequence-design or I/O node such as Load FASTA or FASTA Combiner, connected to the fasta input. You specify the chain ID to rewrite via the target_chain input. The resulting fixed_pdb output, still a dictionary from pdb_name to pdb_content, can then be sent to downstream modeling, scoring, visualization, or export nodes like Save To Zip or Save To Bucket.

This node only changes residue names for ATOM and HETATM records belonging to the specified chain. If the FASTA length does not match the number of residues in that chain, it replaces as many residues as possible and logs a warning. Run it after finalizing the sequence and before tools that expect chemically correct residue types.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A dictionary mapping PDB names to PDB file contents that should be modified. Each value must be a valid PDB-formatted text string with standard ATOM and HETATM records and properly defined chain IDs. The node inspects only those records belonging to the specified target_chain. Empty dictionaries or entries with empty strings are rejected or skipped. Malformed PDB lines, such as truncated records or invalid residue numbers, may cause processing to fail for that structure.</td><td style="word-wrap: break-word;">{ "rfdesign_scaffold.pdb": "ATOM      1  N   GLY B   1      12.345  10.234   8.123  1.00 10.00           N\nATOM      2  CA  GLY B   1      13.111  11.000   8.777  1.00 10.00           C\nTER\nEND\n" }</td></tr>
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA data containing the target one-letter amino acid sequence that will be imposed on the chosen chain. The content may include one or more header lines beginning with a greater-than sign. All non-header lines are concatenated into a single continuous sequence with whitespace removed. The sequence must contain at least one character; empty or header-only FASTA data are rejected. Standard amino acids and common special codes U, O, and X are recognized; unknown characters are left as the original residue type.</td><td style="word-wrap: break-word;">>ProteinMPNN_design_for_chain_B\nMQNKLAVVVDSGRTGKTLLAKILNHYLFNK...</td></tr>
<tr><td style="word-wrap: break-word;">target_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain identifier in the PDB for which residue types will be replaced, typically a single uppercase letter such as A or B. Leading and trailing whitespace is stripped. Only ATOM and HETATM records with this chain ID are modified; all other chains and records remain untouched. If the specified chain is not present in a PDB, processing for that structure fails with a descriptive error.</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A dictionary mapping PDB names to updated PDB contents. For each structure, the three-letter residue names for residues in the specified target_chain are rewritten to match the FASTA sequence, using an internal map from one-letter to three-letter codes such as A to ALA and R to ARG. Residue numbering, atomic coordinates, and all non-target chains are preserved. If the FASTA sequence is shorter than the chain length, only the first positions are updated; if it is longer, extra sequence positions are silently ignored.</td><td style="word-wrap: break-word;">{ "rfdesign_scaffold.pdb": "ATOM      1  N   MET B   1      12.345  10.234   8.123  1.00 10.00           N\nATOM      2  CA  MET B   1      13.111  11.000   8.777  1.00 10.00           C\nTER\nEND\n" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node operates by scanning and rewriting PDB text lines, so runtime scales roughly linearly with total file size. Processing many large PDBs in a single run increases memory use and latency; however, typical single-protein structures are handled quickly.
- **Limitations**: Only residue names are changed. Residue indices, chain IDs, backbone geometry, and side-chain coordinates remain unchanged, which means the resulting structure may be physically unrealistic if the new sequence differs substantially from the original.
- **Behavior**: When the FASTA sequence length differs from the residue count in the target chain, the node logs a warning and replaces residue names only up to the minimum of the two lengths. It does not pad, truncate, or renumber residues to force a match.
- **Behavior**: Unrecognized one-letter amino acid codes that are not in the internal mapping, including characters outside A, R, N, D, C, Q, E, G, H, I, L, K, M, F, P, S, T, W, Y, V, U, O, and X, leave the original residue type in place at that position. A warning is logged for each such character.

## Troubleshooting
- **Common Error 1 – PDB input must be a non-empty dictionary**: This occurs when the pdb field is empty or not a dictionary. Ensure the upstream node outputs a mapping of name to PDB text and that the connection is correctly wired to the pdb input.
- **Common Error 2 – FASTA content cannot be empty or No sequence found in FASTA data**: The FASTA input is empty or contains only header lines. Verify that your FASTA file or upstream node provides at least one non-header line of sequence and that line breaks are preserved.
- **Common Error 3 – Target chain ID cannot be empty or Chain not found in PDB**: Either target_chain is blank or the specified chain does not exist in the PDB. Inspect the PDB to confirm the chain IDs in the ATOM records and update the target_chain value to match exactly.
- **Common Error 4 – Chain fixing failed for PDB**: This generic message indicates an internal parsing or rewriting problem, often due to malformed PDB lines such as truncated columns or non-integer residue numbers. Inspect and clean the PDB with a structure-preparation tool or regenerate it from your modeling software before retrying.
