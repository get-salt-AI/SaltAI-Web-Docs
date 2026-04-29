# PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

PDB Fixer takes one or more PDB structures and a FASTA sequence and rewrites the residue names of a chosen chain so its sequence matches the FASTA. It parses ATOM/HETATM records for the target chain, maps 1-letter amino acid codes from the FASTA to 3-letter PDB residue names, and updates residue names in-place. This is especially useful in RFdiffusion + ProteinMPNN workflows where a designed sequence must be imposed on an existing backbone chain.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbfixernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a PDB backbone and a designed amino acid sequence in FASTA format that should define the residue identities for a specific chain. Typical workflows include: generating a backbone with RFdiffusion (often with a generic GLY chain) and designing a sequence with ProteinMPNN, then using PDB Fixer to apply that designed sequence to the target chain, or correcting/updating a chain’s sequence after in silico mutagenesis planning. Upstream, structures usually come from nodes such as "Load PDB", "Batch PDB", "PDB Combiner", or "PDB Chain Extractor", while sequences come from "Load FASTA" or sequence-design nodes. Downstream, the fixed PDB can be fed into nodes like "PDB To CIF", "CIF To PDB", visualization, or export nodes like "Save To Zip" and "Save To Bucket". For reliable results, make sure the FASTA sequence length closely matches the number of residues in the target chain; otherwise, only the overlapping portion will be replaced and a warning will be logged.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to modify, keyed by structure name. Each value is the full PDB text content. Must be a non-empty dictionary. The PDB lines must follow standard formatting for ATOM/HETATM records so that chain IDs and residue numbers can be parsed correctly.</td><td style="word-wrap: break-word;">{'designed_complex.pdb': 'HEADER    DESIGNED PROTEIN\nATOM      1  N   GLY A   1      ...\nATOM      2  CA  GLY A   1      ...\nATOM      3  N   GLY B   1      ...\nATOM      4  CA  GLY B   1      ...\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA content containing the amino acid sequence that will replace the target chain’s residue types. May include header lines starting with '>'; all non-empty, non-header lines are concatenated into a single string of 1-letter amino acid codes. Content must not be empty and must contain at least one sequence line.</td><td style="word-wrap: break-word;">>designed_chain_B MVLSEGEWQLVLHVWAKVEADVAGHGQDILIRLFKSHPETLEKFDKFKHLK TEAEMKASEDLKKHGATVLTALGGILKKKGHHEAELKPLAQSHATK </td></tr>
<tr><td style="word-wrap: break-word;">target_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID (typically a single character such as 'A' or 'B') whose residue names should be replaced. Whitespace is stripped. If the specified chain does not exist in a PDB, processing for that structure will fail with an error.</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures with the specified chain’s residue names updated to match the FASTA sequence. Keys mirror the input structure names; values are updated PDB contents as strings. Chain IDs, residue numbers, and coordinates remain unchanged; only the 3-letter residue names in the target chain are modified where a valid mapping from the FASTA sequence exists.</td><td style="word-wrap: break-word;">{'designed_complex.pdb': 'HEADER    DESIGNED PROTEIN\nATOM      1  N   MET B   1      ...\nATOM      2  CA  MET B   1      ...\nATOM      3  N   VAL B   2      ...\nATOM      4  CA  VAL B   2      ...\nEND\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Processing is line-based text manipulation and scales roughly linearly with the total number of ATOM/HETATM records across all PDBs; large batches of PDBs will take longer but typically remain efficient.
- **Limitations**: The method assumes a one-to-one correspondence between the FASTA sequence positions and the ordered residues in the target chain. It does not explicitly handle insertions, deletions, or gaps; replacements are truncated to the length of the shorter of the two.
- **Limitations**: Only standard 1-letter amino acid codes are mapped via an internal 1-to-3-letter dictionary (including U→SEC, O→PYL, X→UNK). Characters outside this mapping keep the original residue type and generate a warning.
- **Behavior**: If the FASTA and PDB chain lengths differ, the node logs a warning and replaces only as many residues as both sequences share; extra residues in the PDB or extra characters in the FASTA are ignored for replacement.
- **Behavior**: The node updates residue names in both ATOM and HETATM records for the specified chain but does not modify coordinates, B-factors, occupancy, chain IDs, residue indices, or any records belonging to other chains.

## Troubleshooting
- **Error: PDB input must be a non-empty dictionary**: The `pdb` input is empty, None, or not a dictionary. Ensure you connect an upstream node that outputs a dict like `{ "example.pdb": "<PDB file text>" }`.
- **Error: Target chain ID cannot be empty**: The `target_chain` input is blank or whitespace. Provide a valid chain ID (for example, `A` or `B`) and ensure it matches what is present in the PDB.
- **Error: Chain 'X' not found in PDB**: The specified `target_chain` (e.g., 'B') does not exist in the given PDB. Open the PDB and confirm the chain ID in the ATOM records, then update `target_chain` accordingly.
- **Error: Failed to parse FASTA sequence: No sequence found in FASTA data**: The FASTA input contains only headers or whitespace. Make sure there is at least one line of amino acid letters and that the data is passed into the `fasta` input field.
- **Symptom: Sequence length mismatch warning**: A logged warning about sequence length mismatch indicates the FASTA length and the number of residues in the target chain differ. Check for missing residues or numbering gaps in the PDB, and verify that the FASTA sequence corresponds exactly to the region you intend to replace.
