# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a single molecular sequence specification for Boltz YAML workflows. It can represent proteins, nucleic acids (DNA/RNA), or ligands, including chain assignment, optional duplicated chains, modifications, and polymer-specific options such as MSA and cyclic polymers. The node outputs a list containing one properly structured sequence entry ready for inclusion in a Boltz YAML configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to define one molecular entity (chain) in a Boltz modeling job. For biopolymers (proteins, DNA, RNA), supply the sequence as plain text or FASTA and optionally attach MSA or cyclic flags; for ligands, specify them via SMILES or CCD code. Typically, you will create one Boltz Sequence Builder node per chain or entity, then send all sequence outputs into a list combiner, and finally into the Boltz YAML Combiner before running prediction or other downstream Boltz execution nodes.

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
<tr><td style="word-wrap: break-word;">entity_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Specifies what kind of molecular entity this is, such as protein, DNA, RNA, or ligand. Determines which other fields are relevant and how the node structures the sequence entry.</td><td style="word-wrap: break-word;">protein</td></tr>
<tr><td style="word-wrap: break-word;">sequence_or_fasta</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For proteins, DNA, or RNA, the primary sequence in one-letter code or a full FASTA (header plus sequence). If a FASTA header is present, it may be used as a name or identifier in the Boltz configuration.</td><td style="word-wrap: break-word;">>my_protein\nMSTNPKPQRIT...</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For ligand entities, a SMILES string describing the small molecule. Only used when entity_type is set to ligand and you choose SMILES as the definition method.</td><td style="word-wrap: break-word;">CC(=O)Oc1ccccc1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd_code</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For ligand entities, an optional three-letter CCD (PDB Chemical Component Dictionary) code used instead of SMILES to identify the ligand.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity in the Boltz job. Must be unique across the final YAML; additional identical chains can be provided via a separate duplicate chains setting.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">duplicate_chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING_LIST</td><td style="word-wrap: break-word;">Optional list of extra chain IDs for identical copies of the same entity. The node expands this into multiple chains sharing the same sequence or ligand definition.</td><td style="word-wrap: break-word;">['B', 'C']</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional description or mapping of residue or chemistry modifications applied to the sequence or ligand. The format should follow Boltz YAML expectations, such as position-specific tags or labels.</td><td style="word-wrap: break-word;">N-terminal_acetylation; K27me3</td></tr>
<tr><td style="word-wrap: break-word;">msa_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For proteins and possibly nucleic acids, optional multiple sequence alignment content. This is carried through and later converted into a referenced file by the Boltz YAML Combiner node.</td><td style="word-wrap: break-word;">>seq1\nMSTNPKPQRIT...\n>seq2\nMSTTPKPQKIT...</td></tr>
<tr><td style="word-wrap: break-word;">is_cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Marks polymer entities (protein, DNA, RNA) as cyclic. Boltz workflows may treat cyclic polymers differently during modeling.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional human-readable name or label for this entity. If not provided and a FASTA header exists, the header may be used instead.</td><td style="word-wrap: break-word;">designed_enzyme_v1</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Optional free-form metadata dictionary to attach to this entity, which can be carried through to downstream tools or for record-keeping.</td><td style="word-wrap: break-word;">{"project":"enzyme_screen_01","batch":"B3"}</td></tr>
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
<tr><td style="word-wrap: break-word;">sequence</td><td style="word-wrap: break-word;">BOLTZ_OBJECT_LIST</td><td style="word-wrap: break-word;">A list containing one structured Boltz sequence entry with all specified fields, including type, sequence or ligand description, chain IDs, modifications, and optional MSA or cyclic flags. Designed to be directly combined with other Boltz components into a full YAML configuration.</td><td style="word-wrap: break-word;">[{"type":"protein","chain_ids":["A"],"sequence":"MSTNPKPQRIT...","cyclic":false}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: The node always returns a list, even for a single entity, so it can be directly concatenated with other node outputs in list-combining steps.
- **Note 2**: Chain IDs, including duplicates, must be globally unique once combined for the full Boltz YAML; conflicts will typically be caught by downstream validation nodes.
- **Note 3**: For ligands, you must choose one defining method (SMILES or CCD); providing inconsistent or missing ligand information may lead to validation errors later.
- **Note 4**: If you input a FASTA with a header, that header may override or populate the entity name, depending on configuration; ensure headers are meaningful for your workflow.
- **Note 5**: MSA and other large text attachments are typically converted into files later by the Boltz YAML Combiner; do not assume they remain inline in the final YAML.

## Troubleshooting
- **Issue 1**: Downstream YAML validation fails due to duplicate chain IDs — Review the chain_id and duplicate_chain_ids fields across all sequence or ligand nodes and ensure all final chain labels are unique.
- **Issue 2**: Ligand is missing or mis-specified — Confirm entity_type is set to ligand and that exactly one of ligand_smiles or ligand_ccd_code is correctly filled.
- **Issue 3**: Biopolymer sequence not recognized — Check that the sequence_or_fasta input uses valid one-letter codes and that FASTA formatting, if used, is correct, including line breaks.
- **Issue 4**: MSA not picked up downstream — Ensure msa_content is non-empty text and that the entity_type supports MSA. Verify that you pass this node's output through the Boltz YAML Combiner, which materializes MSA content into files.
- **Issue 5**: Unexpected cyclic behavior — If models behave as if the polymer is closed, verify the is_cyclic flag and set it to false if you intend a linear chain.
