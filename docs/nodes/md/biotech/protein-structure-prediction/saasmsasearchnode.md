# MSA Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs multiple sequence alignment (MSA) searches for one or more protein sequences against managed databases and returns per-sequence alignments in A3M format. Supports MOCK, PROD, and TEST modes, merges results from multiple sources, and performs internal format conversion to produce consolidated A3M MSAs per input sequence.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasmsasearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a protein structure prediction workflow to generate MSAs from input FASTA sequences. Choose a database preset based on speed versus coverage (toy, reduced, full) and set the execution mode (MOCK, PROD, TEST). Connect the msa.a3m output to downstream folding or prediction nodes that require A3M-formatted MSAs.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">One or more protein sequences in FASTA format. Each sequence is processed independently and yields a separate A3M alignment entry keyed by its FASTA header.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQDILDLWQ >seq2 GHHHHHHSSGVDLGTENLYFQSMASMTGGQQ</td></tr>
<tr><td style="word-wrap: break-word;">db_preset</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Selects which MSA database set to search. Controls coverage, speed, and resource usage. Allowed values: alphafold_toy_dataset (very fast, minimal coverage, intended for tests), alphafold_reduced_dataset (balanced speed and coverage), alphafold_full_dataset (maximum coverage with highest latency and resource usage).</td><td style="word-wrap: break-word;">alphafold_reduced_dataset</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Execution mode for the MSA search backend. MOCK returns precomputed mock data without performing real searches, PROD performs real searches against the selected database preset, and TEST forces minimal settings and overrides db_preset to alphafold_toy_dataset for quick checks.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">An MSA-typed object representing a dictionary mapping each input FASTA sequence ID (header) to its merged A3M alignment text. Alignments may be constructed from multiple backend sources and normalized so each sequence has a single A3M block.</td><td style="word-wrap: break-word;">{"seq1": "# A3M content...\n>seq1\nMKTAYI-...", "seq2": ">seq2\nG-HHHS..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: In TEST mode, the db_preset input is ignored and automatically set to alphafold_toy_dataset for faster turnaround.
- **Note 2**: Runtime and timeout risk increase with the number of sequences and heavier presets; alphafold_full_dataset is the slowest and most resource-intensive option.
- **Note 3**: The node merges MSAs from multiple backend sources and converts between Stockholm and A3M internally so each sequence ends up with a single, consolidated A3M alignment.
- **Note 4**: In MOCK mode, no real database queries are executed; outputs are served from bundled mock results and may not reflect the actual input sequences.
- **Note 5**: FASTA headers (the text after ">" on header lines) become keys in the msa.a3m dictionary, so they must be unique and stable to avoid collisions or ambiguous outputs.
- **Note 6**: Sequences that produce no significant hits can still appear in the msa.a3m mapping but may have empty or minimal alignment content.
- **Note 7**: The user-visible output is always A3M text per sequence, even if backend services natively operate with other alignment formats.

## Troubleshooting
- **Issue 1**: Empty or missing A3M for a sequence. Resolution: Verify that the FASTA header is unique and correctly formatted. If coverage is likely the issue, switch to alphafold_reduced_dataset or alphafold_full_dataset and rerun.
- **Issue 2**: Job times out or runs very slowly. Resolution: Reduce the number of sequences processed per run, select alphafold_toy_dataset or alphafold_reduced_dataset, or split your workload into multiple smaller executions.
- **Issue 3**: Output appears to be mock data instead of live results. Resolution: Check the mode input and ensure it is set to PROD rather than MOCK or TEST when you need real database searches.
- **Issue 4**: Downstream node rejects the MSA output. Resolution: Pass the msa.a3m output object directly without restructuring it, and confirm that the downstream node is configured to accept an MSA-typed A3M dictionary.
- **Issue 5**: Confusing or mismatched sequence IDs. Resolution: Inspect FASTA headers, simplify them if needed, and ensure each header is unique since these are used as keys in the output mapping.
- **Issue 6**: Some sequences consistently show no hits. Resolution: Confirm that the sequences are valid proteins and try a larger database preset such as alphafold_full_dataset to improve hit rates; note that some rare or low-complexity sequences may intrinsically have few or no detectable matches.
