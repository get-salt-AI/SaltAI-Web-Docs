# PDB To Fasta

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts a protein structure provided as PDB text into a FASTA-formatted amino acid sequence. You can extract a single chain or all chains, control whether FASTA headers are added, and optionally override automatic headers. Nonstandard or unknown residues are handled conservatively and represented as 'X'.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/biotech/pdbtofastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a protein structure in PDB format and need its amino acid sequence(s) in FASTA format for downstream analysis, alignment, design, or modeling. Typical usage: load or generate a PDB file, pass its full text into this node, specify a chain ID (or leave blank to include all chains), choose header options, then send the resulting FASTA string to sequence-based tools or other biotech nodes.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full PDB file content as a single string. The node parses ATOM records to determine residue order and converts 3-letter residue names into 1-letter amino acid codes.</td><td style="word-wrap: break-word;">ATOM      1  N   MET A   1      11.104  13.207  10.215  1.00 30.00           N  ...</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain identifier to extract (for example, 'A'). If set to an empty string, the node extracts sequences for all chains present in the PDB, each as a separate FASTA entry.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">include_header</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, a FASTA header line (starting with '>') is added for each chain. If false, only the raw sequence lines are output.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">header_prefix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Prefix used when constructing automatic FASTA headers if no custom header is provided. Automatic headers typically follow the pattern '>' + header_prefix + chain_id + '_Protein'.</td><td style="word-wrap: break-word;">Chain_</td></tr>
<tr><td style="word-wrap: break-word;">custom_header</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional custom FASTA header text. When provided and include_header is true, this value is used as the header instead of the automatically generated one (applied to each chain).</td><td style="word-wrap: break-word;">My_Protein</td></tr>
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
<tr><td style="word-wrap: break-word;">fasta</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">FASTA-formatted amino acid sequence or sequences derived from the PDB. Each chain is output as a separate FASTA entry, optionally with a header line, and sequence lines are wrapped at 60 characters. If no valid protein residues are detected, a message indicating that no valid protein sequences were found is returned instead.</td><td style="word-wrap: break-word;">>Chain_A_Protein MSTNPKPQRIT...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Residue parsing**: Only ATOM records are parsed and mapped from 3-letter residue names to 1-letter amino acid codes; each residue number contributes at most one amino acid to avoid duplicates.
- **Residue coverage**: Standard amino acids are supported; SEC is mapped to U, PYL is mapped to O, and any unknown or nonstandard residues are represented as 'X'.
- **Chain handling**: Supplying an empty chain_id includes all chains found in the PDB, each as its own FASTA entry; providing a specific chain_id limits extraction to that chain.
- **Header behavior**: If custom_header is supplied and include_header is true, it overrides the automatically generated header for each chain; otherwise automatic headers use the pattern ">[header_prefix][chain]_Protein".
- **No-sequence case**: If the PDB content has no recognizable protein residues for the requested chain(s), the node returns a clear message instead of an empty FASTA.

## Troubleshooting
- **No valid protein sequences found**: Confirm that pdb_string contains proper ATOM records with standard residue names and that the specified chain_id exists in the file; try leaving chain_id empty to see all chains.
- **Wrong or missing chain in output**: Check that chain_id exactly matches the chain label in the PDB (for example, 'A' or 'B'), and that there are ATOM records for that chain.
- **Headers not appearing**: Make sure include_header is set to true and that you have not unintentionally disabled or overridden headers with an empty or unexpected custom_header value.
- **Only one chain when multiple are expected**: Clear the chain_id input (use an empty string) so the node will include all detected chains instead of filtering to a single one.
- **Unexpected 'X' characters in sequences**: These indicate residues the node cannot confidently map (unknown or modified residues); inspect the corresponding positions in the PDB to verify if they involve nonstandard amino acids or ligands.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/biotech/pdbtofastanode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

