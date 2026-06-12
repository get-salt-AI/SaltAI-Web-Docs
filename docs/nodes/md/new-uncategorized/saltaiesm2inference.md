# ESM-2 Inference

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node runs ESM-2 protein language model inference on one or more protein sequences provided in FASTA format and returns their learned embeddings. It sends the request to a backend inference service, decodes the returned embedding array, and packages the results as a tabular output with one row per sequence. Its main strength is turning raw amino acid sequences into machine-learning-ready numeric representations for downstream biotech analysis.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltaiesm2inference.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need vector embeddings for protein sequences, such as similarity analysis, clustering, retrieval, annotation, or as features for downstream predictive models. It typically appears early in a biotech workflow after a sequence source or FASTA preparation step, and before downstream analysis nodes that consume tabular or embedding data. A common upstream input is a FASTA-producing node or manually prepared FASTA text; common downstream consumers are DataFrame-processing, filtering, export, analytics, or custom ML nodes that operate on per-sequence embeddings. Choose smaller checkpoints for fast iteration and larger checkpoints when embedding quality matters more than runtime; use `cuda` for larger batches and models when GPU infrastructure is available.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequences in FASTA format. The input can contain one or multiple sequences, and each sequence header is used to label the output rows. Sequences should be valid amino acid strings in standard FASTA layout.</td><td style="word-wrap: break-word;">>sp\|P69905\|HBA_HUMAN MVLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDL >sp\|P68871\|HBB_HUMAN MVHLTPEEKSAVTALWGKVNVDEVGGEALGRLLVVYPWTQRFFESFGDLSTPDAVMGNPK</td></tr>
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">["facebook/esm2_t6_8M_UR50D", "facebook/esm2_t12_35M_UR50D", "facebook/esm2_t30_150M_UR50D", "facebook/esm2_t33_650M_UR50D", "facebook/esm2_t36_3B_UR50D", "facebook/esm2_t48_15B_UR50D"]</td><td style="word-wrap: break-word;">Selects the ESM-2 model checkpoint used for inference. Larger checkpoints generally produce richer embeddings but require more time and more memory.</td><td style="word-wrap: break-word;">facebook/esm2_t30_150M_UR50D</td></tr>
<tr><td style="word-wrap: break-word;">batch_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences processed together during inference. Valid range is 1 to 1024. Higher values can improve throughput but also increase memory usage and may cause out-of-memory failures on larger models.</td><td style="word-wrap: break-word;">64</td></tr>
<tr><td style="word-wrap: break-word;">device</td><td>True</td><td style="word-wrap: break-word;">["cpu", "cuda"]</td><td style="word-wrap: break-word;">Chooses the compute device for inference. `cpu` is broadly compatible but slower; `cuda` uses an NVIDIA GPU when available and is much faster for larger models and bigger batches.</td><td style="word-wrap: break-word;">cuda</td></tr>
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
<tr><td style="word-wrap: break-word;">embeddings</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">A table with one row per input sequence. It contains a `sequence_id` column derived from the FASTA headers and an `embedding` column containing the numeric embedding vector for that sequence.</td><td style="word-wrap: break-word;">[{"sequence_id":"sp\|P69905\|HBA_HUMAN","embedding":[0.014,-0.083,0.227,...]},{"sequence_id":"sp\|P68871\|HBB_HUMAN","embedding":[0.031,-0.067,0.205,...]}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Larger checkpoints can be substantially slower and more memory-intensive. Prefer smaller models during development and switch to larger ones only when you need higher-quality embeddings.
- **Performance**: GPU execution via `cuda` is typically much faster for medium and large jobs, but availability depends on backend infrastructure and compatible NVIDIA hardware.
- **Behavior**: The node returns one DataFrame row per FASTA sequence and preserves sequence identifiers from the FASTA headers in the `sequence_id` column.
- **Limitations**: This node outputs embeddings only. It does not directly perform classification, structure prediction, similarity scoring, or annotation without additional downstream processing.

## Troubleshooting
- **Out-of-memory or failed inference**: Reduce `batch_size`, switch to a smaller checkpoint, or move from `cuda` to `cpu` if GPU memory is insufficient.
- **Job takes too long or times out**: Use a smaller checkpoint, reduce the number of sequences per run, or split a large FASTA into smaller batches before inference.
- **Unexpected or missing sequence IDs**: Check FASTA headers carefully. The output `sequence_id` values come from the FASTA input, so malformed or duplicate headers can make downstream mapping difficult.
