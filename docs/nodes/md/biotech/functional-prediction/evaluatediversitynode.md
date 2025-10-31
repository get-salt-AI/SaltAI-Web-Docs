# Evaluate Diversity

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates structural diversity across a set of protein structures and assigns cluster indices based on a TM-score threshold. It can run with configurable parallelism and supports modes for production, testing, and mock data. The result is a CSV summarizing which cluster each input structure belongs to.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/evaluatediversitynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after generating or loading multiple candidate protein structures to identify distinct structural groups. Typical workflows include screening diverse designs, deduplicating similar structures, or selecting representative candidates by cluster.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A collection of protein structures to evaluate. Provide a mapping of names to PDB content for one or more structures.</td><td style="word-wrap: break-word;">{"design_A": "ATOM ...\nEND\n", "design_B": "ATOM ...\nEND\n"}</td></tr>
<tr><td style="word-wrap: break-word;">max_ctm_threshold</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Maximum TM-score threshold used to separate clusters. Lower values yield more clusters (stricter diversity), higher values yield fewer clusters.</td><td style="word-wrap: break-word;">0.6</td></tr>
<tr><td style="word-wrap: break-word;">num_processes</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of processes to use for parallel evaluation. Increase to speed up processing on larger inputs.</td><td style="word-wrap: break-word;">8</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Execution mode. MOCK returns predefined data for demonstration, PROD performs the actual evaluation, TEST uses minimal parameters for faster checks.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">score.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">CSV table with clustering results per input structure (e.g., structure name and assigned cluster index).</td><td style="word-wrap: break-word;">name,cluster_index\ndesign_A,0\ndesign_B,1</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Modes**: MOCK returns canned results; TEST limits to a single structure and sets num_processes to 1 for quick validation; PROD performs the full evaluation.
- **Threshold behavior**: max_ctm_threshold must be within 0.0–1.0 and directly influences how strictly structures are grouped.
- **Parallelism**: num_processes controls concurrency; higher values can reduce runtime but increase resource usage.
- **Input size**: Provide multiple structures to obtain meaningful clustering; a single structure will trivially form one cluster.
- **Output semantics**: The CSV's cluster indices are labels for grouping; the specific numeric values are not ordered by quality.

## Troubleshooting
- **Empty or minimal CSV output**: Ensure the pdb input contains multiple structures; single-structure inputs yield limited clustering information.
- **Unexpectedly few or many clusters**: Adjust max_ctm_threshold. Increase it (closer to 1.0) to merge more structures into fewer clusters; decrease it to split more aggressively.
- **Long runtimes on large batches**: Increase num_processes if resources allow, or use TEST mode to validate configuration before full runs.
- **Results look repeated or identical in MOCK mode**: Switch to PROD mode to run the actual evaluation; MOCK is intended only for demos.
- **Only one structure processed in TEST mode**: This is expected—TEST intentionally limits to the first structure and sets num_processes to 1 for speed.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/functional-prediction/evaluatediversitynode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

