# Boltz (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs single-chain protein structure prediction using the legacy Boltz pipeline. It consumes an A3M multiple sequence alignment and corresponding FASTA to produce ranked 3D structures (PDB) along with model confidence scores. This node is deprecated in favor of Boltz-2 and may be disabled in some environments.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltznode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have generated A3M alignments for one or more sequences. Provide the A3M(s) and set sampling parameters. The node publishes a Boltz prediction job and returns a dict of ranked PDBs per input along with confidence metrics. For new projects, migrate to Boltz-2 for YAML-based configs, ligand/DNA/RNA support, potentials, and affinity prediction.

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
<tr><td style="word-wrap: break-word;">a3m</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Mapping of sequence names to A3M alignment contents for single-chain inputs. Each value should be the plain-text A3M for the corresponding sequence.</td><td style="word-wrap: break-word;">{"my_protein": ">seq1\nMKT...\n>seq2\nMRT..."}</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of structure recycling iterations used by the model. Higher values can improve quality but increase runtime.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples (model runs) to generate diverse candidate structures.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducibility. When multiple inputs are provided, the seed is offset per input to avoid identical samples.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. PROD = normal execution. TEST = overrides to minimal parameters for quick checks. MOCK = returns precomputed mock results when available.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary mapping ranked identifiers to PDB contents for each input. Keys typically include the input name and rank (e.g., my_protein_rank_1.pdb).</td><td style="word-wrap: break-word;">{"my_protein_rank_1.pdb": "ATOM ...", "my_protein_rank_2.pdb": "ATOM ..."}</td></tr>
<tr><td style="word-wrap: break-word;">confidence</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Dictionary of confidence metrics per ranked structure (e.g., pLDDT, PAE). Provided as JSON-serializable data per output structure.</td><td style="word-wrap: break-word;">{"my_protein_rank_1": {"pLDDT": 0.86, "PAE": "..."}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is deprecated. Use Boltz-2 for enhanced functionality, YAML configs, ligand/DNA/RNA support, potentials, and affinity prediction.
- May be disabled in some environments. If disabled, jobs will fail immediately with a message indicating deprecation/availability.
- High recycling_steps and diffusion_samples significantly increase runtime and cost.
- Input must be single-chain. For multimer predictions, use the dedicated multimer node or Boltz-2 with a multimer configuration.
- Mode=TEST forces minimal parameters (e.g., recycling_steps=1, diffusion_samples=1) for quick validation; Mode=MOCK returns canned results if available.

## Troubleshooting
- Job fails immediately with a deprecation/disabled error: This node may be disabled. Switch to Boltz-2 and re-run.
- Empty or malformed A3M input: Ensure the a3m field is a mapping from names to valid A3M text. Regenerate A3Ms using an alignment/search node and retry.
- Very long runtimes: Reduce diffusion_samples and recycling_steps, or use TEST mode to validate the pipeline before full runs.
- No structures returned for some inputs: Verify the corresponding A3M and FASTA are valid and consistent. Recreate alignments and try again.
- Confidence output missing metrics: Downstream services may omit some metrics in test/mock modes. Re-run in PROD mode for full outputs.
