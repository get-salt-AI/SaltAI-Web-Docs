# MSA Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a multiple sequence alignment (MSA) search for one or more protein sequences. It accepts FASTA-formatted input, queries a selected database preset, and returns alignments in A3M format keyed by sequence IDs. Supports production runs, a lightweight test mode, and a mock mode with predefined data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasmsasearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to generate MSA results as a precursor to protein structure prediction or other downstream analyses that require A3M alignments. Provide one or more sequences in FASTA format, choose the database preset based on accuracy/speed needs, and select the appropriate mode (PROD for real runs, TEST for quick checks, MOCK for demos). Feed the resulting A3M output into folding or feature-combination nodes.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">FASTA-formatted sequences to run the MSA search on. Multiple sequences are supported and will be processed individually.</td><td style="word-wrap: break-word;">>protein_1 MSEQNNTEMTFQIQRIYTKDISFEAPNAPHVFQKDW >protein_2 MGSSHHHHHHSSGLVPRGSHMASMTGGQQMGRGS</td></tr>
<tr><td style="word-wrap: break-word;">db_preset</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Selects the MSA database size and coverage. 'alphafold_toy_dataset' is small and fast for debugging, 'alphafold_reduced_dataset' balances speed and coverage, 'alphafold_full_dataset' offers maximum coverage but is slowest.</td><td style="word-wrap: break-word;">alphafold_reduced_dataset</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Execution mode. 'PROD' runs the actual service, 'TEST' uses a lightweight configuration for quick checks, and 'MOCK' returns predefined sample results.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA search results in A3M format, returned as a dictionary mapping each input sequence ID to its A3M alignment.</td><td style="word-wrap: break-word;">{'protein_1': '<a3m_alignment_content>', 'protein_2': '<a3m_alignment_content>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Database preset affects runtime**: Larger presets provide better coverage but take significantly longer.
- **Test mode constraints**: In TEST mode, parameters are adjusted for speed (e.g., smaller datasets), which may reduce coverage.
- **Mock mode**: MOCK mode returns predefined data suitable for demos or pipeline wiring, not for scientific results.
- **Multiple sequences**: If you provide multiple sequences in the FASTA input, each will be processed and returned separately, keyed by its FASTA header ID.
- **Downstream compatibility**: Output is in A3M format and suitable for use in folding/prediction nodes.
- **Input formatting**: Ensure each sequence has a unique FASTA header; malformed FASTA input may lead to empty or failed results.

## Troubleshooting
- **Empty or missing alignments**: Verify your FASTA formatting (headers begin with '>' and sequences are valid amino acid strings). Ensure each header is unique.
- **Long runtimes or timeouts**: Use 'alphafold_toy_dataset' or 'alphafold_reduced_dataset', reduce the number of sequences, or run in TEST mode for quick validation.
- **Unexpected test-like results**: Confirm that 'mode' is set to 'PROD' rather than 'TEST' or 'MOCK'.
- **Downstream node rejects output**: Ensure you pass the entire A3M dictionary output and that downstream nodes expect A3M format.
- **Inconsistent IDs**: Check that FASTA headers (sequence IDs) are correctly set; output keys mirror these IDs.
