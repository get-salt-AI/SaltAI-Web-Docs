# MSA Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs multiple sequence alignment (MSA) searches for one or more protein sequences. Supports different database presets (toy, reduced, full) to balance speed and coverage, and modes for production, mock results, and quick testing. Returns results in A3M format keyed by input sequence IDs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasmsasearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node as the first step in a protein structure prediction workflow to generate A3M MSAs from input FASTA sequences. Typical flow: provide FASTA(s) -> run MSA Search -> feed A3M output into folding nodes (e.g., Alphafold). Choose db_preset based on your accuracy/time needs and mode for development vs production runs.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">One or more protein sequences in FASTA format. If multiple records are provided, each record ID must be unique; outputs will be keyed by these IDs.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQ >seq2 GAVLIPFYWSTCMNQDEKHR</td></tr>
<tr><td style="word-wrap: break-word;">db_preset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MSA database preset to search. Options: alphafold_toy_dataset (small, fast), alphafold_reduced_dataset (balanced), alphafold_full_dataset (comprehensive, slow).</td><td style="word-wrap: break-word;">alphafold_reduced_dataset</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. MOCK: returns predefined sample results. PROD: runs actual MSA search service. TEST: overrides settings for quick runs on a toy dataset.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA results as a dictionary mapping each input sequence ID to its A3M-formatted alignment.</td><td style="word-wrap: break-word;">{"seq1": "# A3M content...\n>seq1\nMKTAYI-...", "seq2": "# A3M content..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- In TEST mode, db_preset is automatically set to alphafold_toy_dataset to speed up runs.
- When multiple sequences are provided in FASTA, the output is a dict keyed by the original FASTA record IDs; ensure IDs are unique and meaningful.
- Runtime scales with both the number of sequences and the chosen database: toy (fast), reduced (moderate), full (slow).
- MOCK mode returns precomputed mock data and does not perform a real search.
- Large database presets can lead to long execution times; plan timeouts and batching accordingly.

## Troubleshooting
- MSA search takes too long: use alphafold_toy_dataset or alphafold_reduced_dataset, or reduce the number/length of input sequences.
- Empty or missing results for a sequence: verify the FASTA formatting and that the sequence ID is unique and non-empty.
- Service timeout: try fewer sequences, switch to a smaller db_preset, or rerun in TEST/MOCK for quick validation.
- Invalid FASTA input: ensure sequences use standard FASTA format with header lines starting with '>' and valid amino acid characters.
