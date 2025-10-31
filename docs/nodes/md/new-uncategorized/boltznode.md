# Boltz (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs legacy Boltz structure prediction on protein sequences using MSA inputs. Generates multiple sampled structures and corresponding confidence/quality metrics. This node is deprecated; use Boltz-2 nodes for enhanced features.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/boltznode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node only for legacy workflows that still depend on the original Boltz service. Provide MSA search results (A3M) for one or more sequences; the node will perform structure prediction with configurable recycling and diffusion sampling, returning ranked PDBs and confidence scores. For new projects, migrate to Boltz2StructurePredictionNode.

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
<tr><td style="word-wrap: break-word;">a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA search results as a dictionary mapping sequence IDs to A3M-formatted strings. Each entry will be processed to predict structures.</td><td style="word-wrap: break-word;">{'seq1': '>seq1\nAAAA...\n>seq1/2-100\n-AAA...', 'seq2': '>seq2\nMKTW...\n>seq2/align\n-MKT...'}</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations during prediction. Higher values can improve accuracy but increase runtime.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of structure samples to generate via diffusion for each input sequence. More samples yield more diverse predictions.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed. If multiple sequences are processed, each uses an incremented seed (seed + index).</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode: MOCK (uses predefined mock outputs), PROD (runs the live service), TEST (overrides key params for quick checks).</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ranked predicted structures as a dictionary mapping '<input_id>_<rank>' to PDB content strings.</td><td style="word-wrap: break-word;">{'seq1_rank_001': 'MODEL 1\nATOM ...\nENDMDL\n', 'seq1_rank_002': 'MODEL 1\nATOM ...\nENDMDL\n'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence_scores.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Confidence and related quality metrics for each predicted structure, keyed by '<input_id>_<metric_or_rank>'.</td><td style="word-wrap: break-word;">{'seq1_rank_001': {'pLDDT_mean': 78.4, 'PAE': [[0.0, 1.2], [1.1, 0.0]]}, 'seq1_rank_002': {'pLDDT_mean': 74.2, 'PAE': [[0.0, 2.0], [2.1, 0.0]]}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Deprecated**: This node is deprecated. Prefer Boltz2StructurePredictionNode for YAML input support, ligands, DNA/RNA, potentials, and affinity prediction.
- **Mode behavior**: TEST mode forces recycling_steps=1 and diffusion_samples=1 to reduce runtime. MOCK mode returns predefined mock results.
- **Multiple inputs**: When multiple sequences are provided, the node processes each and increments the seed per sequence.
- **Performance**: Increasing recycling_steps and diffusion_samples increases runtime and cost.
- **Input expectations**: a3m must be a dict of A3M-formatted strings keyed by sequence IDs; each must contain a valid FASTA header and aligned sequences.

## Troubleshooting
- **Empty or missing outputs**: Ensure 'a3m' is a non-empty dict and each value is valid A3M text with a FASTA header line.
- **Service timeout or long runtime**: Reduce diffusion_samples and recycling_steps, or test with mode='TEST' to validate the pipeline quickly.
- **Inconsistent IDs in results**: Output keys are derived from input sequence IDs; verify your 'a3m' dict keys are unique and meaningful.
- **Unexpectedly few structures**: Check diffusion_samples; in TEST mode it is forced to 1.
- **Reproducibility issues**: Confirm the base seed and remember seeds are incremented per sequence (seed + index).
- **Mock data confusion**: If using mode='MOCK', outputs are fixed from mock data and will not reflect your inputs.
