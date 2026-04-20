# Load FASTA

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

LoadFastaNode ingests a raw FASTA-formatted string and exposes it to the biotech pipeline as a FASTA-typed output. It does not parse, validate, or transform the content; it simply forwards the string and tags it as FASTA. This provides a straightforward entry point for user-provided protein or nucleotide sequences used by downstream biotech nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadfastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use LoadFastaNode when you need to introduce FASTA sequence data into a biotech workflow, for example in AlphaFold-like structure prediction, protein design, or PDB sequence-editing pipelines. Place it at the beginning of the workflow where a user pastes or inputs a FASTA string, then connect its `sequence.fasta` output to nodes that require FASTA, such as PdbFixerNode (to replace a PDB chain sequence with the provided FASTA sequence) or FastaCombinerNode (to merge several FASTA inputs). There is typically no computational upstream node; the input comes directly from user text. Because LoadFastaNode performs no validation, you must ensure that each record is correctly formatted (header line starting with `>` followed by one or more sequence lines). For multi-chain or multi-protein workflows, either put multiple FASTA records into a single string or use multiple LoadFastaNode instances and combine their outputs with FastaCombinerNode.

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
<tr><td style="word-wrap: break-word;">fasta_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw FASTA content to upload. Should contain one or more FASTA records, each with a header line beginning with `>` followed by one or more lines of sequence characters (for example, amino acid one-letter codes). The node does not check syntax or characters, so malformed FASTA (missing headers, empty sequences, inconsistent line breaks, or invalid residue symbols) will be passed downstream unchanged and may cause errors in nodes that parse FASTA.</td><td style="word-wrap: break-word;">>designed_protein_A MKTFFVAVLTLALATASAAPASSSVQAAPVAPAPAPEVQEALKKKVEQLKQQLKQQLEQLKQ QLAQQQQQLEQLKQLLQQLKQQLEQ >designed_protein_B GSSHHHHHHSSGLVPRGSHMGSASGKDVEQLKQLKQLQQQQQQQVQLQQQQQQQLQQQ</td></tr>
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
<tr><td style="word-wrap: break-word;">sequence.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">The same FASTA string supplied in `fasta_string`, now tagged with the FASTA type so biotech nodes recognize it as sequence data. Downstream consumers treat this as FASTA-formatted text; for example, PdbFixerNode parses it to obtain an amino acid sequence to substitute into a PDB chain, and FastaCombinerNode concatenates it with other FASTA inputs. The underlying data is a plain text string containing one or more FASTA records.</td><td style="word-wrap: break-word;">>rf_design_chain_B GGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGG GGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGG</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Execution is effectively instantaneous because the node simply forwards the string and adds type metadata; it introduces no parsing overhead or external service calls, and large sequences are primarily limited by overall workflow memory.
- **Limitations**: LoadFastaNode performs no FASTA validation; problems like missing `>` headers, empty records, or invalid residue characters will only surface when downstream nodes attempt to parse or interpret the data.
- **Behavior**: Multiple FASTA records within a single `fasta_string` are allowed and preserved exactly as provided. How many records are used and which are selected is entirely determined by the consuming node’s logic.
- **Behavior**: The output is a single FASTA-typed string (not a dictionary keyed by IDs as with LoadPDBNode or LoadA3MNode). If you require separately identifiable sequences, use multiple LoadFastaNode instances or encode identity in FASTA headers and manage it downstream.

## Troubleshooting
- **Downstream error like 'FASTA content cannot be empty' or 'No sequence found in FASTA data'**: These messages originate from nodes that parse FASTA (such as PdbFixerNode). Check that `fasta_string` is not empty, that at least one line starts with `>`, and that one or more sequence lines follow.
- **Sequence length mismatch warnings when fixing PDB chains**: If a node such as PdbFixerNode reports that FASTA and PDB residue counts differ, verify that the FASTA sequence passed through LoadFastaNode corresponds exactly to the target chain and does not include extra residues or concatenated records.
- **Unexpected record used from a multi-record FASTA**: If a downstream node appears to use the wrong sequence from several records, confirm the order and headers in `fasta_string`. Some consumers may only use the first record; rearrange the FASTA content or split records across multiple LoadFastaNode instances if necessary.
- **Parsing issues due to hidden or non-printable characters**: Copying sequences from formatted documents or web pages can introduce tabs, carriage returns, or other hidden characters. If downstream parsing behaves unexpectedly, paste the sequence into a plain-text editor, normalize line breaks, remove hidden characters, and then re-enter it into the `fasta_string` field.
