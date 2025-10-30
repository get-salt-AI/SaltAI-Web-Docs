# MSA Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs Multiple Sequence Alignment (MSA) searches for one or more input protein sequences. It splits a multi-record FASTA into individual sequences, performs database searches, and returns alignments in A3M format keyed by sequence IDs. Supports MOCK (precomputed), PROD (full service), and TEST (toy dataset) modes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Protein Structure Prediction/MSASearchNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to prepare A3M alignments before structure prediction nodes like Alphafold or Boltz. Provide one or more sequences in FASTA format. Choose a database preset based on speed vs. coverage needs. In TEST mode, it automatically uses a toy dataset for quick checks. The output maps each input sequence ID to its corresponding A3M alignment.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence(s) in FASTA format. If multiple sequences are provided, each record's ID will be used to key outputs.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQ... >seq2 GHHHHHHSSGLVPRGSHMASMTGGQQMGRGSM...</td></tr>
<tr><td style="word-wrap: break-word;">db_preset</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Database configuration for the MSA search. Choose based on desired speed and coverage.</td><td style="word-wrap: break-word;">alphafold_reduced_dataset</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Execution mode. MOCK uses precomputed data, PROD runs the actual search, TEST forces a toy dataset for quick validation.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA results in A3M format as a dictionary mapping input sequence IDs to their A3M content.</td><td style="word-wrap: break-word;">{"seq1": "...A3M content...", "seq2": "...A3M content..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Mode behavior**: TEST mode overrides db_preset to the toy dataset regardless of input; MOCK returns predefined results for quick demos.
- **Timeout scaling**: Runtime scales with the number of sequences and chosen database. Toy≈300s/seq, Reduced≈3000s/seq, Full≈12000s/seq timeouts.
- **Output keys**: The output dictionary keys match the FASTA record IDs; ensure your IDs are unique and meaningful.
- **Workflow pairing**: The resulting A3M is typically consumed by structure prediction nodes (e.g., Alphafold) that expect A3M per sequence ID.
- **Data handling**: Multi-record FASTA inputs are split automatically and processed in batch.

## Troubleshooting
- **Long runtimes or timeouts**: Use alphafold_toy_dataset for quick checks or alphafold_reduced_dataset for faster runs; split large batches into smaller ones.
- **Empty or missing outputs**: Verify FASTA formatting and that each record includes an ID and valid amino acid sequence.
- **Unexpected TEST results**: Remember TEST mode forces the toy dataset; switch to PROD for real searches.
- **Inconsistent IDs**: Ensure each FASTA record has a unique ID; outputs are keyed by these IDs.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Protein Structure Prediction/MSASearchNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

