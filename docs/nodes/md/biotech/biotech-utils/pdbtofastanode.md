# Pdb To Fasta Node

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts a protein structure provided as PDB text into FASTA sequence(s). It parses ATOM records, maps 3-letter amino acid codes to 1-letter codes, supports selecting a specific chain or extracting all chains, and optionally adds configurable FASTA headers.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbtofastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have PDB content and need the corresponding amino acid sequence(s) in FASTA format for downstream tasks like alignment, design, or structure prediction. Provide the PDB string, optionally target a specific chain, and configure header preferences; the node returns a FASTA-formatted string with line wrapping.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full PDB file content as a string. The node extracts sequences from ATOM records.</td><td style="word-wrap: break-word;">ATOM      1  N   MET A   1 ... (full PDB text)</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain identifier to extract (e.g., A). Leave empty to extract sequences for all chains found.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">include_header</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to include a FASTA header line for each chain.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">header_prefix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Prefix used to build the header when include_header is true and no custom header is provided. Format becomes >{prefix}{chain}_Protein.</td><td style="word-wrap: break-word;">Chain_</td></tr>
<tr><td style="word-wrap: break-word;">custom_header</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional custom header text. If provided and include_header is true, the header will be >{custom_header} instead of using the chain-based default.</td><td style="word-wrap: break-word;">MyProtein</td></tr>
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
<tr><td style="word-wrap: break-word;">fasta</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">FASTA-formatted sequence(s) extracted from the PDB. For multiple chains, sequences are concatenated one after another, each optionally with its own header. Lines are wrapped at 60 characters.</td><td style="word-wrap: break-word;">>Chain_A_Protein MSEQNNTEMTFQIQRIYTKDIS... (wrapped at 60 chars)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain selection**: If chain_id is empty, the node extracts sequences for all chains present; otherwise only the specified chain is returned.
- **Residue mapping**: Standard 3-letter amino acids are mapped to 1-letter codes. Special cases include SEC -> U, PYL -> O, and unknown UNK -> X.
- **Parsing behavior**: Only ATOM records are considered. Each residue is added once per residue number to avoid duplication across atoms.
- **Headers**: If include_header is true and custom_header is provided, the header is exactly that value. Otherwise, the header is composed as >{header_prefix}{chain}_Protein.
- **Line wrapping**: Output sequences are wrapped at 60 characters per line.
- **Empty results**: If no valid residues are found for the specified chain(s), the output will indicate that no valid protein sequences were found.

## Troubleshooting
- **No valid protein sequences found**: Confirm the PDB content includes ATOM lines for amino acid residues and that the chain_id exists. If targeting a chain, try leaving chain_id empty to include all chains.
- **Unexpected 'X' characters**: These correspond to residues labeled UNK or unmapped 3-letter codes. Ensure your PDB uses standard residue names or adjust upstream processing.
- **Wrong chain extracted**: Verify the exact chain identifier from the PDB (column 22). Chain IDs are case-sensitive.
- **Missing headers in output**: Ensure include_header is true. If you expected a custom header, confirm custom_header is set and non-empty.
- **Formatting looks off**: Line breaks are inserted every 60 characters by design. If you need a single line, post-process the output to remove line wraps.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbtofastanode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

