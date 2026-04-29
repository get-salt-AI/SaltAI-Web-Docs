# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node creates one Boltz-compatible molecular sequence object that can represent proteins, nucleic acids (DNA/RNA), or ligands. For polymers it accepts raw or FASTA-formatted sequences, infers names and optional MSA metadata, and supports residue-level modifications and cyclic polymers. For ligands it validates and records either SMILES or CCD codes. The output is a list containing one sequence mapping that plugs directly into downstream Boltz list/YAML combiners.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to define a single molecular entity for a Boltz-based modeling workflow, and you want one flexible node that can handle proteins, nucleic acids, and ligands. For proteins/DNA/RNA, provide the sequence either directly as a string (optionally as FASTA with a header) or via a FASTA-typed input, and optionally attach an MSA (for proteins), residue modifications, and whether the polymer is cyclic. For ligands, provide either a SMILES or CCD code but not both. Assign a primary chain ID and, if the same entity should appear multiple times, specify additional IDs via the multiple_chains string.

Typical workflow: connect Boltz Sequence Builder for each entity in your system (e.g., receptor protein, peptide, DNA, ligand), then feed their outputs into a Boltz List Combiner node to merge them into a single list. That combined list is then passed into a Boltz YAML Combiner node, which produces the final Boltz YAML and auxiliary files consumed by Boltz Predict. Related nodes include BoltzProteinSequenceNode and BoltzLigandSequenceNode (specialized versions of this builder), BoltzConstraintNode for structural constraints, BoltzTemplateNode for structural templates, BoltzPropertyNode for affinity/property specifications, BoltzListCombinerNode to aggregate multiple objects, and BoltzYAMLCombinerNode plus BoltzPredictNode for running the Boltz engine.

Best practices: keep all chain IDs unique across all sequence nodes that will be combined in a single YAML; use multiple_chains only for truly identical copies. For proteins with homologous information, attach an MSA via the msa input to improve predictions. Use FASTA headers without commas if you care about human-friendly sequence names; otherwise, the node will generate generic names.

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
<tr><td style="word-wrap: break-word;">entity_type</td><td>True</td><td style="word-wrap: break-word;">['protein', 'dna', 'rna', 'ligand']</td><td style="word-wrap: break-word;">The type of molecular entity to define. Determines which other fields are required and how the output is structured. For 'protein', 'dna', and 'rna' you must provide a sequence (direct string or FASTA). For 'ligand' you must provide either a SMILES or CCD code.</td><td style="word-wrap: break-word;">protein</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity in the Boltz system. Must be unique across all sequences in a given Boltz YAML. For polymers, this is the chain label; for ligands, it is the ligand chain. Case-sensitive and typically one letter, but can be multi-character if consistent.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Amino acid or nucleotide sequence as a plain string or FASTA text for protein/dna/rna entity types. If FASTA-style (starting with '>'), the node will extract the header (first word) as _sequence_name and strip commas, then concatenate the sequence lines. If plain, it is used as-is and a default name like 'protein_sequence' is assigned. Required if sequence_fasta is not provided.</td><td style="word-wrap: break-word;">>my_protein MSTNPKPQRIT...KDEL</td></tr>
<tr><td style="word-wrap: break-word;">sequence_fasta</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Sequence in FASTA format supplied via a dedicated FASTA-typed input (e.g., from a loader node) for protein/dna/rna. The node picks the first entry, using its key as the sequence name (with commas removed) and the associated value as the sequence content. If provided, this takes precedence over sequence.</td><td style="word-wrap: break-word;">{"my_protein": ">my_protein\nMSTNPKPQRIT...KDEL"}</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Multiple sequence alignment for protein entities, typically in A3M or CSV format. The node inspects and normalizes the content using infer_msa_content_and_format, storing both the processed content and an _msa_format tag. Leave empty to use an 'empty' MSA, which is acceptable but may give weaker predictions.</td><td style="word-wrap: break-word;">>seq1 MSTNPKPQRIT... >seq2 MSTNPKP-RIT...</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string for ligand entities when entity_type is 'ligand'. Mutually exclusive with ligand_ccd; providing both will raise an error. Must be a valid small-molecule SMILES representation accepted by Boltz.</td><td style="word-wrap: break-word;">CC(=O)NC1=CC=C(O)C=C1</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Three-letter CCD (Chemical Component Dictionary) code for ligand entities when entity_type is 'ligand'. Mutually exclusive with ligand_smiles. Use when the ligand corresponds to a standard PDB chemical component.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue-level modifications for protein/dna/rna sequences. Enter one modification per line in the format 'position:ccd_code', where position is a 1-based integer residue index and ccd_code is a valid CCD identifier. Lines without ':' or with a non-integer position are ignored with a warning. Parsed entries are attached as a 'modifications' list of {position, ccd}.</td><td style="word-wrap: break-word;">5:MSE 10:SEP</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Marks polymer sequences (protein/dna/rna) as cyclic when True. When set, a 'cyclic': true flag is added for the entity, which Boltz can use to handle head-to-tail connectivity.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs for identical copies of this entity (e.g., homomeric complexes). These IDs are combined with the primary chain_id into an 'id' list; if only one ID exists, 'id' is stored as a single string. All IDs must be unique across the full Boltz YAML.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">sequences</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single Boltz sequence mapping. For polymers the element looks like {'protein'\|'dna'\|'rna': {'id': 'A' or ['A','B'], 'sequence': '...', optional keys: '_sequence_name', 'msa', '_msa_format', 'modifications', 'cyclic'}}. For ligands it looks like {'ligand': {'id': 'L' or ['L','M'], and exactly one of 'smiles' or 'ccd'}}. This structure is designed to be merged with other sequences via BoltzListCombinerNode and then consumed by BoltzYAMLCombinerNode.</td><td style="word-wrap: break-word;">[{"protein": {"id": ["A", "B"], "sequence": "MSTNPKPQRIT...KDEL", "_sequence_name": "my_protein", "msa": "msa_1.a3m", "_msa_format": "a3m", "modifications": [{"position": 5, "ccd": "MSE"}], "cyclic": false}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Parsing MSAs and large sequences is lightweight, but very large MSA inputs (tens of thousands of sequences) will increase memory and file size downstream; consider truncating MSAs before passing them in.
- **Limitations**: For ligand entities you must provide exactly one of ligand_smiles or ligand_ccd; providing neither or both raises a validation error.
- **Behavior**: When a FASTA header is used, only the first whitespace-separated token is kept as the sequence name and any commas are removed; the rest of the header is discarded.
- **Behavior**: If multiple_chains is set, the 'id' field becomes a list of chain IDs, otherwise it is a single string; downstream nodes that validate uniqueness (like BoltzYAMLCombinerNode) will fail if any ID is reused.
- **Limitations**: For protein/dna/rna, sequence input is strictly required (sequence or sequence_fasta). An empty or missing sequence will raise a ValueError before any YAML is produced.

## Troubleshooting
- **Missing polymer sequence**: Error "Sequence is required for protein" (or dna/rna) occurs when both sequence and sequence_fasta are empty. Provide a valid sequence string or connect a FASTA input.
- **Conflicting ligand specification**: Error "Provide either SMILES or CCD code, not both" occurs when both ligand_smiles and ligand_ccd are non-empty for a ligand. Clear one of the two fields so only a single representation is used.
- **Invalid modification format**: If a line in modifications lacks ':' or uses a non-integer position, the node logs a warning like "Invalid modification format: 10A:MSE" and silently skips that entry. Fix the line to "10:MSE" to include it.
- **Downstream duplicate chain IDs**: If different BoltzSequenceNode instances reuse the same chain ID (including via multiple_chains), BoltzYAMLCombinerNode will later fail with a duplicate chain ID error. Ensure all chain_id and multiple_chains values are globally unique across the workflow.
