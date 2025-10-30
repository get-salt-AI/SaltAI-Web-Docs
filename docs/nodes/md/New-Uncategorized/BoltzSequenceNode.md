# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a single molecular sequence specification entry for use in Boltz YAML pipelines. Supports proteins, nucleic acids (DNA/RNA), and ligands, with options for chain IDs, multiple identical chains, MSAs, modifications, and cyclic polymers. Produces a structured sequence object encapsulated in a list, ready to be combined with other Boltz components.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/BoltzSequenceNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define one entity (protein, DNA, RNA, or ligand) in a Boltz workflow. For proteins/nucleic acids, provide the sequence as plain text or FASTA; for proteins, optionally include an MSA. For ligands, specify either a SMILES string or a CCD code. The output can be merged with other sequences or constraints and passed to downstream Boltz nodes that assemble YAML and run predictions.

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
<tr><td style="word-wrap: break-word;">entity_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Type of molecular entity to create: protein, dna, rna, or ligand.</td><td style="word-wrap: break-word;">protein</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence for protein/dna/rna. Accepts plain sequence or FASTA-formatted text; if FASTA, the header is used to derive a sequence name.</td><td style="word-wrap: break-word;">>MyProt MSTNPKPQRIT...</td></tr>
<tr><td style="word-wrap: break-word;">sequence_fasta</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Sequence provided as a FASTA file/object for protein/dna/rna. Alternative to the sequence text input.</td><td style="word-wrap: break-word;">{ "MyProt": ">MyProt\nMSTNPKPQRIT..." }</td></tr>
<tr><td style="word-wrap: break-word;">msa_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Multiple sequence alignment (MSA) content as a plain string for proteins. Leave empty to use an explicit 'empty' MSA.</td><td style="word-wrap: break-word;">>seq1\nMSTN-...\n>seq2\nMSAN-...</td></tr>
<tr><td style="word-wrap: break-word;">msa_a3m</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA in A3M format for proteins. Alternative to msa_content.</td><td style="word-wrap: break-word;">{ "alignment.a3m": ">seq1\nMSTN-...\n>seq2\nMSAN-..." }</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string for ligand entities. Provide this or ligand_ccd, not both.</td><td style="word-wrap: break-word;">CC(=O)Oc1ccccc1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand CCD (Chemical Component Dictionary) code. Provide this or ligand_smiles, not both.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Post-translational or chemical modifications for protein/dna/rna. One per line in the form position:ccd_code.</td><td style="word-wrap: break-word;">5:SEP 12:MLZ</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Marks the polymer as cyclic for protein/dna/rna.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional chain IDs for identical copies of this entity, comma-separated. The entity will be duplicated across these chain IDs.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">sequences</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single sequence specification dict for the chosen entity type, ready to be combined into Boltz YAML by downstream nodes.</td><td style="word-wrap: break-word;">[{ "protein": { "id": ["A","B"], "sequence": "MSTN...", "_sequence_name": "MyProt", "msa": "empty", "modifications": [{"position":5,"ccd":"SEP"}], "cyclic": false } }]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Entity type drives required fields**: protein/dna/rna require a sequence; ligand requires either ligand_smiles or ligand_ccd.
- **FASTA support**: If the sequence begins with '>', it is parsed as FASTA. The header (without commas) becomes _sequence_name and the sequence lines are concatenated.
- **Protein MSAs**: Provide msa_a3m or msa_content; if neither is provided, an explicit "empty" MSA is set.
- **Ligand exclusivity**: Do not supply both ligand_smiles and ligand_ccd; exactly one must be provided.
- **Multiple chains**: The id field becomes an array when multiple_chains is provided; otherwise it is a single chain ID string.
- **Modifications format**: Each line must be position:ccd_code with an integer position; invalid lines are ignored with a warning.
- **Cyclic flag**: Applies only to polymer entities (protein/dna/rna).
- **Validation errors**: Missing required values or conflicting ligand inputs will raise errors.

## Troubleshooting
- **Error: Sequence is required for protein/dna/rna**: Provide sequence in either the sequence field or sequence_fasta.
- **Error: SMILES or CCD code is required for ligands**: Set ligand_smiles or ligand_ccd (but not both).
- **Error: Provide either SMILES or CCD code, not both**: Remove one of the two ligand inputs.
- **Protein MSA not appearing**: Ensure msa_a3m is a valid A3M object or msa_content is non-empty; otherwise it defaults to "empty".
- **Modification ignored**: Check the format is position:ccd_code and that position is an integer (e.g., 12:MLZ).
- **Unexpected chain IDs**: Trim spaces and separate multiple chains with commas only (e.g., B,C).
