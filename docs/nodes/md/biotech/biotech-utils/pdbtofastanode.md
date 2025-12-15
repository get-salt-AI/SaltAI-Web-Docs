# PDB To Fasta

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts a PDB structure (as text) into a FASTA-formatted amino acid sequence. You can target a specific chain or extract all chains, and optionally control FASTA headers and formatting. Nonstandard residues are handled conservatively and unknowns are represented as 'X'.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbtofastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a protein structure in PDB format and need the corresponding amino acid sequence in FASTA format for downstream analysis or modeling. Typical workflow: load or generate a PDB, pass its text content here, optionally specify a chain (or leave empty to extract all), and feed the resulting FASTA to alignment, design, or prediction nodes.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full PDB file content as a string. The node parses ATOM records to infer the sequence.</td><td style="word-wrap: break-word;">ATOM      1  N   MET A   1 ...</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain identifier to extract (e.g., 'A'). Leave empty to extract all chains.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">include_header</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prepend a FASTA header line for each chain.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">header_prefix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Prefix used when constructing automatic headers if no custom header is provided.</td><td style="word-wrap: break-word;">Chain_</td></tr>
<tr><td style="word-wrap: break-word;">custom_header</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional custom FASTA header text. If set, it replaces the automatic header for each chain.</td><td style="word-wrap: break-word;">My_Protein</td></tr>
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
<tr><td style="word-wrap: break-word;">fasta</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">FASTA-formatted sequence(s) extracted from the PDB. Multiple chains are output sequentially, each optionally with its own header, and sequences are wrapped at 60 characters per line.</td><td style="word-wrap: break-word;">>Chain_A_Protein MSTNPKP... </td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node parses residues from PDB ATOM records and maps 3-letter amino acid codes to 1-letter codes.
- Supported mappings include standard residues (e.g., ALA->A, GLY->G) plus SEC->U, PYL->O, and unknowns as X.
- Duplicates are avoided by tracking residue numbers; only one letter per residue is emitted.
- If chain_id is empty, sequences for all chains found are included, each as a separate FASTA entry.
- If no valid protein residues are found, the node returns a message indicating no valid sequences were found.
- Headers: if custom_header is provided, it overrides the automatic header; otherwise headers use the pattern '>[header_prefix][chain]_Protein'.
- Line wrapping is applied at 60 characters per sequence line.

## Troubleshooting
- If output says 'No valid protein sequences found.': ensure pdb_string contains valid ATOM records and standard residue names, and that the selected chain exists.
- If the wrong chain is extracted: verify the chain_id matches the PDB's chain column (e.g., 'A', 'B').
- If headers are missing: set include_header to true, or remove custom_header if it's unintentionally blanking your expected format.
- If multiple chains are needed but only one appears: leave chain_id empty to include all chains.
- If unexpected 'X' characters appear: the PDB likely contains unknown or nonstandard residues at those positions.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbtofastanode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

