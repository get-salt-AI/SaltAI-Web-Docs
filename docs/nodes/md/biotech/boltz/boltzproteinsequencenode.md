# Boltz Protein Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a protein sequence specification object for Boltz YAML workflows from a protein FASTA input. It parses the FASTA to extract the sequence and optional header (as the sequence name), and can attach optional MSA data, residue modifications, a cyclic flag, and multiple chain IDs. The result is a structured protein sequence payload ready to be merged into a full Boltz YAML configuration for downstream modeling or prediction.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzproteinsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to define a protein entity in a Boltz-based modeling workflow, such as structure prediction or affinity estimation. Provide your protein as a FASTA string, optionally include MSA, modifications, and whether the protein is cyclic, and assign one or more chain IDs if the sequence should appear multiple times. Connect this node's output into a Boltz List Combiner, then into a Boltz YAML Combiner, and finally into a Boltz prediction or diffusion node along with other entities like ligands, templates, constraints, and properties.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Protein FASTA content as text. The node extracts the main sequence and, if present, uses the FASTA header line (after the leading '>') as the sequence name. Supports a single-sequence FASTA; additional metadata lines are ignored.</td><td style="word-wrap: break-word;">>MyProtein\nMKTFFVAGILLLGVALGANA\n</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional multiple sequence alignment data associated with the protein. Typically an aligned FASTA or similar text representation compatible with Boltz. When provided, it is attached to the protein entry for improved modeling quality.</td><td style="word-wrap: break-word;">>seq1\nMKTFFVAGILLLGVALGANA\n>seq2\nMKTIYVAGILLLGVALGANA\n</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional textual description of residue-level modifications to apply to the protein, such as post-translational modifications or mutations, using whatever encoding the Boltz backend expects.</td><td style="word-wrap: break-word;">A15C, S23p, M45(oxidized)</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Marks the protein as cyclic when set to "true", indicating that the N- and C-termini are linked. This affects how the modeling engine treats connectivity.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">primary_chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Main chain identifier assigned to this protein sequence in the Boltz complex, such as "A". Should be unique among all primary entities unless intentionally duplicated through additional chains.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">additional_chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma- or space-separated list of extra chain IDs for duplicated copies of the same protein sequence, such as "B,C". Each additional chain ID represents another identical chain in the complex.</td><td style="word-wrap: break-word;">B,C</td></tr>
<tr><td style="word-wrap: break-word;">sequence_name_override</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional explicit sequence name to use instead of deriving it from the FASTA header. Useful when the header is missing, generic, or not meaningful.</td><td style="word-wrap: break-word;">Receptor_Domain_1</td></tr>
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
<tr><td style="word-wrap: break-word;">protein_sequence_entry</td><td style="word-wrap: break-word;">BOLTZ_SEQUENCE_LIST</td><td style="word-wrap: break-word;">A list containing one Boltz-compatible protein sequence specification object. This object encodes the parsed sequence, name, chain IDs, and any optional MSA, modifications, or cyclic flag, ready to be merged with other entries by Boltz list and YAML combiner nodes.</td><td style="word-wrap: break-word;">[{"type":"protein","name":"MyProtein","sequence":"MKTFFVAGILLLGVALGANA","chains":["A","B"],"msa":"...aligned sequences...","modifications":"A15C","cyclic":false}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **FASTA expectations**: Only the first sequence in the FASTA is used. The leading '>' header line is optional but, when present, becomes the default sequence name unless overridden.
- **Chain ID handling**: The primary_chain_id must be provided. additional_chain_ids create identical extra chains from the same sequence; ensure there are no unintended chain ID collisions with other sequence or ligand nodes.
- **Optional data impact**: MSA and modifications are optional but can strongly influence model quality and interpretation. Ensure they are consistent with the primary sequence and with what your Boltz backend supports.
- **Cyclic setting**: Use the cyclic flag only for truly cyclic proteins or macrocycles. Incorrectly marking a linear protein as cyclic can lead to unrealistic models.
- **Workflow role**: This node produces a partial configuration, not a complete job. It must be combined with other sequence, ligand, template, constraint, and property nodes, then passed through Boltz list and YAML combiners before prediction.

## Troubleshooting
- **Issue: No sequence in output.** Check that the FASTA field is non-empty, contains at least one sequence line, and that line breaks are correctly preserved.
- **Issue: Chain ID conflicts downstream.** Review all sequence and ligand nodes to ensure each chain ID is unique unless you deliberately model multiple identical copies of the same entity.
- **Issue: Backend rejects MSA or modifications.** Confirm that the MSA format and modification syntax match what your Boltz deployment expects and that indexing aligns with the sequence.
- **Issue: Unexpected topology for cyclic proteins.** If results look wrong, verify that cyclic is set appropriately, and test with cyclic set to "false" to compare behavior.
- **Issue: Confusing or generic names in outputs.** Provide sequence_name_override to impose a clear, human-readable name instead of relying on the FASTA header.
