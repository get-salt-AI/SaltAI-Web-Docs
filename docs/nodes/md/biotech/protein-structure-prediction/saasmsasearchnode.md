# MSA Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs multiple sequence alignment (MSA) searches for one or more protein sequences. It queries managed databases, merges results from multiple sources, and returns per-sequence alignments in A3M format. Supports MOCK, PROD, and TEST modes, including automatic format conversion and result consolidation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasmsasearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a protein structure prediction workflow to generate MSAs from input FASTA sequences. Select a database preset based on speed vs. coverage needs (toy, reduced, or full). Feed the resulting A3M output into downstream folding/prediction nodes.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">One or more protein sequences in FASTA format. Each sequence will be processed independently and returned as a separate A3M entry.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQDILDLWQ >seq2 GHHHHHHSSGVDLGTENLYFQSMASMTGGQQ</td></tr>
<tr><td style="word-wrap: break-word;">db_preset</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Which database set to search. Allowed values: alphafold_toy_dataset (fast, minimal), alphafold_reduced_dataset (balanced), alphafold_full_dataset (maximum coverage).</td><td style="word-wrap: break-word;">alphafold_reduced_dataset</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Execution mode. MOCK uses bundled mock data, PROD calls the live service, TEST forces minimal settings for quick checks.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Dictionary mapping each input sequence ID to its merged A3M alignment text.</td><td style="word-wrap: break-word;">{"seq1": "# A3M content...\n>seq1\nMKTAYI-...", "seq2": ">seq2\nG-HHHS..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- In TEST mode, the database preset is automatically overridden to alphafold_toy_dataset for faster turnaround.
- Timeouts scale with the number of input sequences and the selected database preset (toy < reduced < full). Very large batches can take a long time.
- Results from multiple sources are merged: Stockholm-formatted MSAs are aggregated and converted to A3M per sequence.
- If a source returns A3M, it is internally converted to Stockholm before merging to ensure consistent processing.
- MOCK mode serves precomputed responses from bundled mock data and does not perform real searches.
- Ensure unique FASTA headers; output keys mirror the input sequence IDs.
- The node returns an A3M dictionary even if some sequences have no hits; such entries may be empty.

## Troubleshooting
- Empty or missing A3M for a sequence: Verify the sequence ID is unique and valid. Try a larger database preset (reduced or full) for better coverage.
- Timeouts or job takes too long: Reduce batch size (fewer sequences per run), use alphafold_toy_dataset or alphafold_reduced_dataset, or run sequences in separate executions.
- Unexpected MOCK-like results: Confirm mode is set to PROD (not MOCK or TEST).
- Downstream node rejects output: Ensure you're passing the MSA (A3M) dictionary directly; do not modify keys or formats.
- Mismatched sequence IDs: Check FASTA headers for duplicates or unusual characters; headers become dictionary keys in the output.
