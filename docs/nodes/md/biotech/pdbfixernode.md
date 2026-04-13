# PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Replaces the residue identities of a specific chain in one or more PDB structures using a provided FASTA sequence. It preserves atom coordinates, atom counts, and residue numbering, changing only the three-letter residue codes in the target chain. If the FASTA sequence length differs from the chain length, it replaces as many residues as possible and leaves the remaining residues unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/biotech/pdbfixernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to retarget a designed backbone (for example, a glycine-only or placeholder chain from backbone generation) with a desired amino-acid sequence for downstream design, modeling, or scoring workflows. Typical integration: load or generate PDB structures into a PDB dictionary; prepare a FASTA sequence for the chain you want to modify; specify the chain identifier; run PDB Fixer; then feed the fixed PDB dictionary into subsequent nodes such as visualization, combiners, or structure design tools.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Structured PDB input, usually a dictionary mapping PDB IDs (strings) to PDB text contents. For each entry that contains the specified chain_id, the node updates residue three-letter codes in that chain according to the FASTA sequence while leaving coordinates and numbering unchanged.</td><td style="word-wrap: break-word;">{ "design_001": "ATOM      1  N   GLY A   1 ...\nTER\nEND\n" }</td></tr>
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA-formatted amino-acid sequence specifying the desired residue identities for the target chain. Only the sequence characters are used for mapping; the header is optional for this node but must follow standard FASTA formatting if present.</td><td style="word-wrap: break-word;">>chainA_seq\nACDEFGHIKLMNPQRSTVWY\n</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain identifier within the PDB structures that should be modified (for example, "A"). Only residues in this chain are changed; other chains remain untouched.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">allow_length_mismatch</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Optional flag controlling how differences between the PDB chain length and FASTA length are handled. When true, the node replaces residues only up to the minimum of the two lengths and leaves extra residues unchanged. When false, a significant mismatch may result in an error or the node refusing to process the structure.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">PDB dictionary with the same keys and atom coordinates as the input but with updated three-letter residue codes for the specified chain based on the FASTA sequence. All non-target chains and structural details are preserved.</td><td style="word-wrap: break-word;">{ "design_001": "ATOM      1  N   ALA A   1 ...\nTER\nEND\n" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Residue-only modification**: The node changes only residue identities (three-letter codes) for the specified chain and does not alter coordinates, atom counts, or residue numbering. It does not rebuild side chains or optimize geometry.
- **Handling of length differences**: When length mismatch handling is enabled, only the overlapping portion of the chain and FASTA is updated. Remaining residues keep their original identities, which can yield partially updated chains if lengths differ.
- **Per-chain targeting across a batch**: For batch inputs, each PDB entry is processed independently using the same FASTA and chain_id. Structures that lack the specified chain_id are passed through unchanged.
- **Sequence validity**: The FASTA sequence must contain standard amino-acid one-letter codes. Non-standard or ambiguous characters can lead to skipped residues or errors depending on validation behavior.
- **Header is not used for mapping**: The FASTA header line does not need to match any PDB ID; only the sequence lines determine residue identities in the target chain.

## Troubleshooting
- **Issue: Output PDB looks unchanged.** Verify that chain_id exactly matches the chain label in the PDB (including case). Confirm that the connected pdb input contains the structures you expect and that the FASTA is non-empty and properly formatted.
- **Issue: Length mismatch error or warning.** Either enable allow_length_mismatch (if you accept partial replacement) or adjust the FASTA sequence length to better match the number of residues in the target chain.
- **Issue: Downstream tools report poor side-chain geometry.** This node only renames residues and does not perform structural relaxation. Pass the output into a refinement or side-chain rebuilding tool to obtain physically realistic conformations.
- **Issue: Only some entries in a batch are modified.** Check whether each PDB entry actually contains the specified chain_id. Entries missing that chain will remain unchanged by design.
- **Issue: FASTA parsing or invalid residues.** Ensure the FASTA starts with a header line beginning with '>' followed by lines of valid one-letter amino-acid codes, with no spaces or unsupported symbols in the sequence.
