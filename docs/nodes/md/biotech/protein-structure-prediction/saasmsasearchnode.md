# MSA Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a Multiple Sequence Alignment (MSA) search for one or more protein sequences and returns results in A3M format. Supports different database presets to trade off speed vs. coverage and includes modes for production, quick testing, and mock outputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasmsasearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to generate A3M MSAs from FASTA sequences as the first step in a protein structure prediction workflow. Provide a single- or multi-record FASTA; the node will split and process each sequence, returning an A3M per sequence ID. Feed the resulting A3M into downstream folding nodes (e.g., Alphafold).

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">One or more protein sequences in FASTA format. Multiple records are supported and will be processed independently using their FASTA headers as IDs.</td><td style="word-wrap: break-word;">>seq1 MKTIIALSYIFCLVFADYKDDDDK >seq2 GAMGKKVADALTNAVGDKL...</td></tr>
<tr><td style="word-wrap: break-word;">db_preset</td><td>True</td><td style="word-wrap: break-word;">['alphafold_toy_dataset', 'alphafold_reduced_dataset', 'alphafold_full_dataset']</td><td style="word-wrap: break-word;">Selects the database size used for the MSA search. Toy is small and fast (for debugging), Reduced is a smaller version of large MSA databases (faster than Full), and Full offers maximum coverage.</td><td style="word-wrap: break-word;">alphafold_reduced_dataset</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">['MOCK', 'PROD', 'TEST']</td><td style="word-wrap: break-word;">Execution mode. PROD performs a real search, TEST forces minimal parameters for quick runs using the toy dataset, and MOCK returns predefined example results.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A dictionary mapping each input sequence ID to its A3M-formatted MSA result.</td><td style="word-wrap: break-word;">{"seq1": "# A3M content for seq1...", "seq2": "# A3M content for seq2..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Database size impacts runtime**: Larger presets (Reduced, Full) can significantly increase search time; use Toy for debugging or quick iterations.
- **TEST mode behavior**: Regardless of the selected preset, TEST mode forces the toy dataset to speed up execution.
- **Multi-FASTA handling**: The node splits multi-record FASTA input and returns an A3M entry per FASTA header ID.
- **MOCK mode**: Returns predefined mock A3M data for rapid prototyping; results will not reflect the actual sequences.
- **Output format**: Results are A3M text blocks keyed by sequence IDs, suitable for structure prediction nodes.
- **Sequence quality**: Ensure valid protein sequences (standard amino acid letters) and proper FASTA headers for best results.

## Troubleshooting
- **Long runtimes or timeouts**: Switch to 'alphafold_toy_dataset' or 'TEST' mode for faster runs, or reduce the number of input sequences.
- **Empty or missing outputs**: Verify the FASTA is valid, contains at least one sequence with a proper header, and uses standard amino acid characters.
- **Unexpected IDs in results**: Ensure each FASTA record has a unique header; IDs are derived from headers.
- **Downstream node rejects A3M**: Confirm the output is passed directly as the A3M dictionary and not as raw text; each key should be a sequence ID with its A3M content.
- **Inconsistent behavior between modes**: Remember that TEST forces the toy dataset and MOCK returns predefined data; use PROD for real searches.
