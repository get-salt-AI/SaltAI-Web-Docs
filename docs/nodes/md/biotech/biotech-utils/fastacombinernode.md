# FASTA Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

FASTA Combiner merges multiple FASTA-formatted sequence inputs into one contiguous FASTA string. It accepts up to five optional FASTA inputs and concatenates all non-empty ones, inserting newline separators only when needed. This is useful when you want to provide several independently generated or loaded FASTA sequences as a single sequence input to downstream biotech nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/fastacombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple FASTA sources and a downstream node expects a single FASTA string. Typical upstream nodes include LoadFastaNode (user-entered or pasted sequences), PDBToFastaNode (sequences extracted from PDB structures), or other custom nodes that output type FASTA. A common workflow is: LoadPDBNode → PDBToFastaNode (for one or more structures) → FastaCombinerNode → sequence-based modeling or design nodes. The node trims leading and trailing whitespace from each provided FASTA and concatenates them in order from fasta_1 to fasta_5, separating blocks with a newline if required. Ensure each input is a valid FASTA text (headers starting with '>' and sequences on subsequent lines) because this node does not validate or fix formatting; it just passes the text through after trimming.

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
<tr><td style="word-wrap: break-word;">fasta_1</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">First optional FASTA input. Should be a valid FASTA string, possibly containing one or more records. Leading and trailing whitespace are stripped before combination.</td><td style="word-wrap: break-word;">>protA_chain_A MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQ >protA_chain_B GHHHHHHSSGLVPRGSHMASMTGGQQMGRGSM</td></tr>
<tr><td style="word-wrap: break-word;">fasta_2</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Second optional FASTA input. If provided and non-empty, its trimmed content is appended after fasta_1, separated by a newline if needed.</td><td style="word-wrap: break-word;">>protB MVLSEGEWQLVLHVWAKVEADVAGHGQDILIRLFKSHPETLEKF KFKHLKSEDEMKASEDLKKHGATVLTAL</td></tr>
<tr><td style="word-wrap: break-word;">fasta_3</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Third optional FASTA input. Behaves the same as fasta_1 and fasta_2; it is concatenated if non-empty.</td><td style="word-wrap: break-word;">>designed_linker GGGSGGGSGGGSGGGSGGGS</td></tr>
<tr><td style="word-wrap: break-word;">fasta_4</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fourth optional FASTA input for additional sequences. Order in the combined output follows the numeric suffix of the inputs.</td><td style="word-wrap: break-word;">>nanobody_seq QVQLVESGGGLVQPGGSLRLSCAASGFTFSTYAMGWFRQAPGKERE</td></tr>
<tr><td style="word-wrap: break-word;">fasta_5</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fifth optional FASTA input. If you need more than five FASTA sources, pre-combine some upstream before feeding them into this node.</td><td style="word-wrap: break-word;">>scaffold_protein MHHHHHHENLYFQGSMASMTGGQQMGRGSEFDDDDEEELVELGKKLLEAARAGQDDE</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">A single FASTA string that contains all non-empty input FASTA contents concatenated in order from fasta_1 to fasta_5. Each block is trimmed and separated by at most one newline, preserving valid FASTA record boundaries if inputs are well-formed.</td><td style="word-wrap: break-word;">>protA_chain_A MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQ >protA_chain_B GHHHHHHSSGLVPRGSHMASMTGGQQMGRGSM >protB MVLSEGEWQLVLHVWAKVEADVAGHGQDILIRLFKSHPETLEKF KFKHLKSEDEMKASEDLKKHGATVLTAL >designed_linker GGGSGGGSGGGSGGGSGGGS</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs simple string concatenation in memory and is efficient even for large FASTA inputs; overall performance is dominated by the size of the sequences.
- **Limitations**: If all provided FASTA inputs are empty, whitespace-only, or unconnected, the node raises a ValueError indicating that at least one FASTA input is required and produces no output.
- **Behavior**: Each non-empty input is stripped of leading and trailing whitespace; if the current combined string does not end with a newline before appending the next block, a newline is inserted automatically to avoid merging the end of one sequence line directly with the next header.
- **Behavior**: The node does not parse, validate, or rename FASTA headers. Any issues such as duplicate headers, unsupported residues, or malformed lines will only surface in downstream consumers.

## Troubleshooting
- **Common Error 1**: ValueError stating that at least one FASTA input is required – This occurs when none of fasta_1–fasta_5 contain non-empty text. Connect at least one FASTA-producing node such as LoadFastaNode or PDBToFastaNode and ensure it outputs a non-empty sequence.
- **Common Error 2**: Downstream tool reports malformed FASTA or invalid residues – Inspect each upstream FASTA input for proper headers ('>'), allowed residue characters, and line breaks. Since this node only concatenates trimmed text, any upstream formatting problems are propagated unchanged.
- **Common Issue 3**: Headers and sequences appear visually merged in the combined output – Check that individual FASTA inputs either end with a newline or that the downstream viewer expects the exact combined layout. The combiner ensures there is at most one newline between concatenated blocks, but if an input is missing final newlines inside its own content, headers and sequences might still appear cramped.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/fastacombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

