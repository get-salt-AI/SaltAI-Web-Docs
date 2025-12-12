# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Constructs a single molecular sequence specification in the Boltz YAML format. Supports protein, DNA, RNA, and ligand entities, including optional metadata such as MSA, sequence names (from FASTA), chain multiplicity, polymer cyclicity, and residue modifications. Returns a list containing one sequence block, ready to be combined with other Boltz components.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define one entity in a Boltz workflow. For proteins/DNA/RNA, provide a raw sequence or FASTA; for ligands, provide either a SMILES string or a CCD code. Optionally include MSA for proteins and mark polymers as cyclic. To create multimers of identical chains, specify additional chain IDs. Connect the output to a Boltz List Combiner and then to the Boltz YAML Combiner.

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
<tr><td style="word-wrap: break-word;">entity_type</td><td>True</td><td style="word-wrap: break-word;">["protein", "dna", "rna", "ligand"]</td><td style="word-wrap: break-word;">Type of molecular entity to define.</td><td style="word-wrap: break-word;">protein</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw amino acid or nucleotide sequence for protein/DNA/RNA. Use this or sequence_fasta.</td><td style="word-wrap: break-word;">MKTAYIAKQRQISFVKSHFSRQDILDLWQ...</td></tr>
<tr><td style="word-wrap: break-word;">sequence_fasta</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA-formatted sequence for protein/DNA/RNA. Header is used to set a sequence name. Use this or sequence.</td><td style="word-wrap: break-word;">>my_protein MKTAYIAKQRQISFVKSHFSRQDILDLWQ</td></tr>
<tr><td style="word-wrap: break-word;">msa_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MSA content as plain text for proteins. If omitted and no msa_a3m is provided, an 'empty' MSA is set.</td><td style="word-wrap: break-word;">>seq1 AAAA- >seq2 AA-A-</td></tr>
<tr><td style="word-wrap: break-word;">msa_a3m</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA in A3M format for proteins. Alternative to msa_content.</td><td style="word-wrap: break-word;">{'msa_file.a3m': '>seq1\nAAAA-\n>seq2\nAA-A-'}</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand specification via SMILES. Provide this or ligand_ccd (not both).</td><td style="word-wrap: break-word;">CC(=O)Oc1ccccc1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand specification via PDB CCD three-letter code. Provide this or ligand_smiles (not both).</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue modifications for polymers, one per line as 'position:ccd_code'.</td><td style="word-wrap: break-word;">5:PHA 12:MSE</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Mark the polymer as cyclic.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated additional chain IDs to create identical copies of this entity.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">sequences</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one Boltz sequence mapping for the selected entity type.</td><td style="word-wrap: break-word;">[{'protein': {'id': 'A', 'sequence': 'MKTAYIAKQRQ...', '_sequence_name': 'my_protein', 'msa': 'empty'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Protein/DNA/RNA require a sequence via 'sequence' or 'sequence_fasta'.
- Ligands require exactly one of 'ligand_smiles' or 'ligand_ccd'.
- For proteins, if no MSA is provided (msa_content or msa_a3m), the node sets msa to 'empty'.
- FASTA headers are used to set _sequence_name (commas are removed).
- Use 'multiple_chains' to replicate the same entity across several chain IDs (e.g., A plus B,C).
- Downstream YAML validation requires all chain IDs to be unique across all sequences.

## Troubleshooting
- Error: 'Sequence is required for protein/dna/rna' — Provide a non-empty sequence via 'sequence' or 'sequence_fasta'.
- Error: 'SMILES or CCD code is required for ligands' — Set one of 'ligand_smiles' or 'ligand_ccd'.
- Error: 'Provide either SMILES or CCD code, not both' — Remove one of the ligand inputs.
- Unexpected missing name — If using FASTA, ensure the header line starts with '>' to capture a sequence name.
- Invalid modifications ignored — Ensure each modification line is 'position:ccd_code' with an integer position.
- Duplicate chain IDs downstream — Ensure unique chain IDs across all sequences before combining in the Boltz YAML Combiner.
