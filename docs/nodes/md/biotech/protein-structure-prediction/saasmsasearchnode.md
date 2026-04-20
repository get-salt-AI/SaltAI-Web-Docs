# MSA Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node sends one or more protein sequences in FASTA format to Salt's biotech MSA search backend and gathers hits from multiple databases. It merges Stockholm-format MSAs from all sources, normalizes them, and converts the final alignment for each input sequence into A3M format. It supports MOCK, PROD, and TEST modes and scales timeouts with database size and number of sequences.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasmsasearchnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need multiple sequence alignments as input to downstream structure prediction (for example, before `SaaSAlphafoldNode`) or evolutionary analyses. Place `SaaSMSASearchNode` early in your biotech workflow: upstream, sequences typically come from a sequence import or design node that outputs FASTA; downstream, `SaaSAlphafoldNode` consumes the resulting MSA dict in A3M format. Common patterns: (1) Single protein structure prediction: FASTA source → `SaaSMSASearchNode` → `SaaSAlphafoldNode`; (2) Panel screening: provide a multi-entry FASTA and run one MSA search for many targets; (3) Fast debugging: use TEST or MOCK mode with toy data to validate the pipeline without long runtimes. Prefer `alphafold_reduced_dataset` in PROD for a balance of coverage and performance; reserve `alphafold_full_dataset` for final high-coverage runs. During development, use `mode=TEST` to reduce cost and runtime, and only use `mode=MOCK` when you explicitly want deterministic, non-biological example data.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">One or more protein sequences in standard FASTA format. Multi-entry FASTA is automatically split and each entry is processed as a separate sequence, keyed by its header. Headers must start with '>' and should be unique; sequences should contain valid amino acid characters on subsequent lines.</td><td style="word-wrap: break-word;">>P53_HUMAN MEEPQSDPSVEPPLSQETFSDLWKLLPENNVLSPLPSQAMDDLMLSPDDIEQWFTEDPGP >MY_PROTEIN_1 MKTAYIAKQRQISFVKSHFSRQDILDLLR</td></tr>
<tr><td style="word-wrap: break-word;">db_preset</td><td>True</td><td style="word-wrap: break-word;">["alphafold_toy_dataset", "alphafold_reduced_dataset", "alphafold_full_dataset"]</td><td style="word-wrap: break-word;">Controls which database preset is used for MSA search. `alphafold_toy_dataset` is a very small test dataset for quick debugging and has low coverage. `alphafold_reduced_dataset` uses a reduced BFD configuration that is much smaller than full but still gives good coverage. `alphafold_full_dataset` uses the full database stack for maximum coverage at the cost of much longer runtimes and higher resource usage.</td><td style="word-wrap: break-word;">alphafold_reduced_dataset</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">["MOCK", "PROD", "TEST"]</td><td style="word-wrap: break-word;">Execution mode. `PROD` runs against the live SaaS MSA search service using the selected `db_preset`. `TEST` internally forces `db_preset` to `alphafold_toy_dataset` and uses minimal parameters for quick end-to-end tests. `MOCK` bypasses external services and instead serves precomputed example MSAs from local mock JSON, providing deterministic but non-biological output.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">A dictionary mapping each input sequence ID (FASTA header) to its merged multiple sequence alignment in A3M format. Internally, the node collects Stockholm (and any A3M) results from all underlying MSA sources, converts formats as needed, merges them, and returns a single A3M alignment string per sequence. Additional metadata such as configuration and an output folder name (e.g., "msa_search") is attached by the platform.</td><td style="word-wrap: break-word;">{"P53_HUMAN":"P53_HUMAN/1-393\nMEEPQSDPSVEPPLSQETFSDLWKLLPENNVLSPLPSQAMDDLMLSPDDIEQWFTEDPGP\n>seq_2\nMEEPQSDPSEEPPLSQETFSDLWKLLPENNVLSPLPSQAMDDLMLSPDDIEQWFTEEPGP\n","MY_PROTEIN_1":"MY_PROTEIN_1/1-90\nMKTAYIAKQRQISFVKSHFSRQDILDLLR...\n>homolog_1\nMKTAYIAKQRESFVKSHFSRQDILDLVR...\n"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Runtime grows with both the number of input sequences and the chosen `db_preset`. `alphafold_full_dataset` can be very slow for large batches; prefer `alphafold_reduced_dataset` for most day-to-day work.
- **Performance**: The node scales its backend timeout roughly with sequence count and database size (for example, toy on the order of minutes per sequence, reduced and full significantly higher). Very large FASTA batches can still exceed overall workflow limits.
- **Limitations**: In `MOCK` mode, the node reads static mock MSAs from a JSON file and adjusts only headers; these outputs are not biologically meaningful and must not be used for scientific conclusions.
- **Limitations**: In `TEST` mode the node ignores the user-selected `db_preset` and always uses the toy dataset internally, which can lead to shallow alignments if you forget to switch back to PROD for final runs.
- **Behavior**: Multi-entry FASTA input is split per header and stored in a dict keyed by header lines; duplicate or malformed headers may lead to overwritten or missing keys in the output.
- **Behavior**: The node normalizes MSA responses from multiple services (Stockholm or A3M) by converting to Stockholm where needed and then back to A3M after merging, so downstream nodes always see A3M-formatted alignments.

## Troubleshooting
- **Common Error 1**: Workflow run appears to hang or exceed time limits when using `alphafold_full_dataset` with many sequences. Fix by reducing the number of sequences per run, switching to `alphafold_reduced_dataset`, or first validating with `mode=TEST` and a smaller FASTA.
- **Common Error 2**: One or more input headers are missing in the output MSA dictionary. Check FASTA formatting (unique headers, proper '>' lines) and confirm that sequences are valid proteins; sequences with no detectable homologs may also yield empty or absent alignments.
- **Common Error 3**: Downstream `SaaSAlphafoldNode` predictions look poor despite correct configuration. Verify that this node is running in `PROD` and not in `MOCK` or `TEST`, and that `db_preset` is at least `alphafold_reduced_dataset`. Re-run on a single sequence to rule out formatting issues.
- **Common Error 4**: Backend errors or no response from the MSA search service. Try re-running with a very small, known-good FASTA in `TEST` mode. If the issue affects PROD only, capture the `workflow_execution_id` and timestamp and share them with support for investigation.
