# PDB To Fasta

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts one or more PDB structures into FASTA sequences. It parses ATOM records, maps 3-letter residue codes to 1-letter amino acids, and outputs one FASTA record per chain. You can restrict extraction to specific chain IDs or process all chains.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbtofastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have protein structures (PDB) and need corresponding amino-acid sequences for downstream bioinformatics tasks (e.g., MSA generation, sequence design, or annotation). Typical workflow: load or generate PDB(s) -> PDB To Fasta -> combine or feed the resulting FASTA into alignment or modeling tools.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more PDB files provided as a structured input. Each entry is treated as a separate structure and parsed for chain sequences.</td><td style="word-wrap: break-word;">{'example.pdb': 'ATOM      1  N   MET A   1      11.104  13.207  10.118  1.00 20.00           N  \nATOM      2  CA  MET A   1      12.560  13.332  10.420  1.00 20.00           C  \n...'}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs to extract (e.g., "A,B"). Leave empty to extract all chains present in the PDB(s).</td><td style="word-wrap: break-word;">A,B</td></tr>
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
<tr><td style="word-wrap: break-word;">seqs.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA-formatted sequences, one record per chain from each provided PDB. Headers include the PDB entry name and chain ID.</td><td style="word-wrap: break-word;">>example.pdb_chain_A MSTNPKPQRIT...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only residues found in ATOM records are parsed; hetero atoms and non-standard residues are not included unless mapped. UNK is mapped to X.
- Chain filtering is applied before extraction; leave the 'chains' input empty to process all chains.
- If no valid sequence is found for the specified chains, the node raises an error.
- Output headers follow the format ">{pdb_name}_chain_{chain}", and sequences are wrapped at 60 characters per line.
- Residues are added once per residue number per chain to avoid duplicates from multiple atoms in a single residue.

## Troubleshooting
- No output or error: Ensure the specified chain IDs exist in the PDB (check the chain column). If unsure, leave 'chains' empty to include all chains.
- Unexpected amino acids as X: The PDB may contain unknown or unrecognized residue names (UNK) or modified residues that are not mapped.
- Very short or empty sequences: Confirm the input is a valid PDB with ATOM records for protein residues; missing ATOM lines or non-protein entries will not yield sequences.
- Multiple PDBs input but fewer outputs than expected: Some files may not contain the requested chains; verify chain availability per file.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbtofastanode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

