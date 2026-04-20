# PDB To Fasta

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node extracts a protein sequence from a PDB structure and outputs it in FASTA format. It parses ATOM records, maps 3‑letter residue codes to 1‑letter codes, and optionally constructs a header line for the chosen chain. It is useful when you need a clean amino‑acid sequence derived directly from a structural model.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/biotech/pdbtofastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a protein structure in PDB text form and need the corresponding amino‑acid sequence as FASTA, for example before running sequence‑based tools, MSA pipelines, or sequence design workflows. It typically sits downstream from loaders or generators that produce PDB content (e.g. “Load PDB”, “PDB Combiner”, “PDB Fixer”) and upstream of sequence‑driven nodes like “Load FASTA”, “FASTA Combiner”, or any node expecting a raw sequence string. Configure the chain identifier to focus on a specific chain from multimeric structures, and control whether to include a FASTA header or supply your own header line. A common pattern is: LoadPDBNode → PDBToFastaNode → MSA/Search or design/inference nodes. For best results, ensure the PDB is cleaned and contains standard residue names so that residues map correctly to 1‑letter codes using the node’s internal amino‑acid dictionary.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full PDB file contents as a single text string. Must include standard ATOM records with 3‑letter residue names (e.g. ALA, GLY) and chain identifiers. The node will scan this string to extract the sequence for the selected chain.</td><td style="word-wrap: break-word;">HEADER    EXAMPLE PDB ATOM      1  N   MET A   1      11.104  13.207   5.342  1.00 20.00           N ATOM      2  CA  MET A   1      12.560  13.400   5.600  1.00 20.00           C ATOM      3  N   GLY B   1      18.201  10.734   3.120  1.00 20.00           N TER END </td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The single‑character PDB chain identifier whose sequence should be extracted. Only residues belonging to this chain are converted to the FASTA sequence.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">include_header</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to include a standard FASTA header line (starting with '>') in the output. If true, the node will prepend a header constructed from the header_prefix and/or custom_header; if false, it returns only the raw sequence.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">header_prefix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Prefix used when auto‑generating the FASTA header if include_header is true and no custom_header is supplied. Typically combined with the chain_id, e.g. 'Chain_' → '>Chain_A'.</td><td style="word-wrap: break-word;">Chain_</td></tr>
<tr><td style="word-wrap: break-word;">custom_header</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional custom FASTA header text (without the leading '>'). If provided and include_header is true, this replaces the default generated label. If empty, the node falls back to header_prefix + chain_id.</td><td style="word-wrap: break-word;">designed_enzyme_variant_7_chain_A</td></tr>
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
<tr><td style="word-wrap: break-word;">fasta</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single FASTA‑formatted string containing the sequence for the selected chain. If include_header is true, the first line is a header starting with '>', followed by one or more lines of uppercase 1‑letter amino‑acid codes. Non‑standard residues are mapped via the internal dictionary, with unknowns typically represented as 'X'.</td><td style="word-wrap: break-word;">>Chain_A MKTAYIAKQRQISFVKSHFSRQDILD </td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node operates on raw text and is fast even for large PDBs; overhead is dominated by the size of the PDB string, not external services.
- **Limitations**: Only residues with recognizable 3‑letter codes in the internal AA dictionary are converted cleanly; unusual or modified residues may become 'X' in the output sequence.
- **Limitations**: If the specified chain_id is absent in the PDB, the resulting FASTA may be empty or the node may raise an error depending on upstream validation.
- **Behavior**: Header construction depends on include_header, header_prefix, and custom_header—if include_header is false, no header is emitted even if custom_header is set.
- **Behavior**: The node does not reorder or renumber residues; it follows the order encountered in the PDB file for the selected chain, ignoring gaps in residue numbering.

## Troubleshooting
- **Empty or very short FASTA output**: This often means the chain_id does not exist in the PDB or only HETATM/modified residues are present. Confirm the chain identifiers in the PDB (columns for chain ID) and update the chain_id input.
- **Unexpected 'X' characters in sequence**: Residues mapped to 'X' are not recognized in the internal amino‑acid dictionary (e.g. non‑standard or modified residues). Consider preprocessing the PDB to standardize residue names or accept that these positions are ambiguous.
- **Malformed FASTA (no header when expected)**: If you expect a FASTA header but only see the sequence, check that include_header is true. If a specific label is needed, set custom_header rather than relying only on header_prefix.
- **Error parsing PDB string**: If the node reports issues reading the PDB, verify that pdb_string contains valid ATOM records in standard PDB column layout and that the text is not truncated or in CIF/other formats.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/biotech/pdbtofastanode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

