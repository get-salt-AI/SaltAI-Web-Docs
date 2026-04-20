# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a normalized sequence or ligand record in the Boltz YAML format from user-provided molecular information. It supports proteins, nucleic acids (DNA/RNA), and ligands, handling FASTA or plain sequences, optional MSAs, modifications, cyclicity, and multiple identical chains. The output is a one-element list of entity dictionaries ready to be merged into larger Boltz sequence collections.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to define one molecular entity (chain or ligand) for a Boltz-based structural modeling or design job. Typical scenarios include specifying a protein chain with sequence and MSA for structure prediction, adding DNA/RNA chains to nucleic acid or protein–nucleic acid complexes, or describing a small-molecule ligand using SMILES or CCD codes. This node usually appears early in a biotech workflow: upstream, you may prepare inputs with nodes that load or generate FASTA or sequence text, MSA objects, or ligand descriptors; downstream, you typically aggregate multiple outputs (lists of sequences) into a combined sequence list for a Boltz job submission or pass them into higher-level Boltz job or complex-building nodes. For proteins, pair it with nodes that generate or import FASTA data and MSA objects (for example, an MSA builder or file loader node). For ligands, combine it with nodes that load ligand data from external databases or design tools. Best practice is to clearly separate different entity types (protein, dna, rna, ligand) into separate invocations of this node, and then combine the resulting lists in a later aggregation step. Always ensure chain IDs are unique across your complex, and use the multiple_chains field when you want identical copies of the same entity without redefining the sequence.

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
<tr><td style="word-wrap: break-word;">entity_type</td><td>True</td><td style="word-wrap: break-word;">STRING (choice: protein \| dna \| rna \| ligand)</td><td style="word-wrap: break-word;">Type of molecular entity to build. Determines which other inputs are required and how the internal Boltz YAML entry is structured.</td><td style="word-wrap: break-word;">protein</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity. For polymers, becomes the chain label (for example, "A"). For ligands, it identifies the ligand instance. Must not be empty; should be unique per entity unless multiple_chains is used.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Amino acid or nucleotide sequence as plain text, or optionally a single-entry FASTA string. Used for protein, dna, or rna when sequence_fasta is not supplied. If the string starts with '>', it is parsed as FASTA and the header is used to derive a sequence name; otherwise a default name like 'protein_sequence' is assigned. Required for protein/dna/rna if sequence_fasta is not provided.</td><td style="word-wrap: break-word;">MKTAYIAKQRQISFVKSHFSRQDILD</td></tr>
<tr><td style="word-wrap: break-word;">sequence_fasta</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Sequence object in FASTA format, preferred for protein/dna/rna. The node expects a mapping from sequence name to sequence string and will take the first entry, using the key (with commas stripped) as _sequence_name. If provided, this takes precedence over the plain sequence field.</td><td style="word-wrap: break-word;">{ "protein_chain_A": "MKTAYIAKQRQISFVKSHFSRQDILD" }</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Multiple sequence alignment object (A3M or CSV-like) used only for protein entity_type. The node infers content and format and stores both. If left empty, downstream nodes may treat it as an empty or default MSA.</td><td style="word-wrap: break-word;">{ "format": "a3m", "content": ">seq1\nMKTAYIAKQRQISFVKSHFSRQDILD\n>seq2\nMKTTYIAKQRIISFVKSHFSRQDILE" }</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string describing a ligand, used only when entity_type is "ligand". Mutually exclusive with ligand_ccd: you must provide exactly one of them. Cannot be empty if ligand_ccd is empty.</td><td style="word-wrap: break-word;">CC1=CC(=O)OC2=CC=CC=C21</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand identifier from the Chemical Component Dictionary (CCD), used only when entity_type is "ligand". Mutually exclusive with ligand_smiles. Cannot be empty if ligand_smiles is empty.</td><td style="word-wrap: break-word;">HEM</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Polymer modifications for protein/dna/rna as multi-line text. Each line must be "position:ccd_code", where position is a 1-based integer and ccd_code is a CCD identifier. Invalid lines are skipped with a warning.</td><td style="word-wrap: break-word;">5:MSE 12:SEP</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Marks the polymer as cyclic when true. Applies to protein/dna/rna entities; ignored for ligands.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs (for example, "B,C,D") that are identical copies of the same entity. When set, the internal id field becomes a list of all chain IDs instead of a single string.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">sequences</td><td style="word-wrap: break-word;">LIST of DICT (Boltz entity records)</td><td style="word-wrap: break-word;">A one-element list containing the constructed entity description in Boltz YAML schema form. The dictionary has a single top-level key equal to entity_type (protein, dna, rna, or ligand) whose value contains fields like id (string or list of chain IDs), sequence (for polymers), optional _sequence_name, optional msa and _msa_format (for proteins), modifications (list of {position, ccd}), cyclic (boolean), or ligand fields (smiles or ccd). This list is designed to be concatenated with other such lists to form the full sequence specification for a Boltz job.</td><td style="word-wrap: break-word;">[{"protein": {"id": ["A", "B"], "sequence": "MKTAYIAKQRQISFVKSHFSRQDILD", "_sequence_name": "protein_chain_A", "msa": "MSA_FILE_CONTENT_OR_OBJECT", "_msa_format": "a3m", "modifications": [{"position": 5, "ccd": "MSE"}], "cyclic": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs only lightweight parsing and dictionary construction; it is inexpensive compared to downstream structure prediction or design nodes and can be used freely in larger graphs.
- **Limitations**: For protein, dna, or rna, either sequence_fasta or sequence must be provided; otherwise the node raises a ValueError indicating that a sequence is required for the chosen entity type.
- **Limitations**: For ligands, exactly one of ligand_smiles or ligand_ccd must be non-empty; providing both or neither results in a ValueError about mutually exclusive or missing ligand descriptors.
- **Behavior**: When sequence is entered as a FASTA-style string (starting with ">"), the header is parsed to generate a sequence name and the actual sequence lines are extracted; if the sequence is plain text, a generic name like "protein_sequence" is used.
- **Behavior**: The id field in the output is a single string when only chain_id is given, but becomes a list of chain IDs when multiple_chains is populated; downstream nodes must handle both cases.
- **Behavior**: Modification lines that do not match "integer:ccd_code" are ignored with a logged warning, so malformed entries will be dropped rather than stopping the workflow.

## Troubleshooting
- **Missing sequence for protein/dna/rna**: An error message such as "Sequence is required for protein" indicates that neither sequence_fasta nor a non-empty sequence string was provided. Supply a valid FASTA object or plain sequence string, or ensure upstream nodes are connected.
- **Conflicting ligand inputs**: The error "Provide either SMILES or CCD code, not both" occurs when both ligand_smiles and ligand_ccd are non-empty for a ligand entity. Clear one of them so that only a single ligand descriptor is used.
- **No ligand descriptor provided**: The error "SMILES or CCD code is required for ligands" means both ligand_smiles and ligand_ccd are empty for entity_type = "ligand". Enter an appropriate SMILES string or CCD code.
- **Malformed modifications**: If some modification lines are ignored and the resulting modifications list is incomplete, check that each line follows "position:ccd_code" with a valid integer position (for example, "15:MSE") and no extra characters.
- **Unexpected structure of FASTA or MSA inputs**: If sequence names or MSA formats seem wrong downstream, verify that the FASTA and MSA inputs follow the expected object structure, such as a mapping from name to sequence for FASTA and appropriate format indicators for MSA, as produced by compatible upstream nodes.
