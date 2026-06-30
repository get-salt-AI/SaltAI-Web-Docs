# ESM-2 Inference

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node runs ESM-2 inference on one or more protein sequences provided in FASTA format and returns pooled sequence embeddings. It outputs a DataFrame with one row per FASTA record, preserving sequence IDs from FASTA headers and attaching each embedding as a numeric vector.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltaiesm2inference.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need numerical protein representations for downstream analysis, clustering, similarity search, classification, or machine-learning workflows. It is typically placed after a FASTA-producing or FASTA-cleaning node, such as nodes that load protein sequences, generate sequences, or extract FASTA text from biotech pipelines. The output works well with table-oriented downstream nodes such as DataFrame to CSV, DataFrame columns/list extraction, DataFrame join/concatenation nodes, or custom analysis nodes that consume DATAFRAME values. For exploratory work, start with `facebook/esm2_t6_8M_UR50D` on CPU for fast feedback; for higher-quality embeddings, choose a larger checkpoint and use CUDA when available. Keep FASTA headers unique because they become `sequence_id` values in the output, and tune `batch_size` based on available memory: larger batches improve throughput but increase RAM/VRAM use.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequences in FASTA format. Each record should have a unique header and a non-empty amino-acid sequence. Multiple FASTA records are supported and produce one embedding row per record.</td><td style="word-wrap: break-word;">>spike_RBD_variant_A NITNLCPFGEVFNATRFASVYAWNRKRISNCVADYSVLYNSASFSTFKCYGVSPTKLNDLCFTNVYADSFVIRGDEVRQIAPGQTGKIADYNYKLPDDFTGCVIAWNSNNLDSKVGGNYNYLYRLFRKSNLKPFERDISTEIYQAGSTPCNGVEGFNCYFPLQSYGFQPTNGVGYQPYRVVVLSFELLHAPATVCGPKKST</td></tr>
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">ESM-2 model checkpoint to use. Available options are `facebook/esm2_t6_8M_UR50D`, `facebook/esm2_t12_35M_UR50D`, `facebook/esm2_t30_150M_UR50D`, `facebook/esm2_t33_650M_UR50D`, `facebook/esm2_t36_3B_UR50D`, and `facebook/esm2_t48_15B_UR50D`. Larger models usually provide richer embeddings but require more time and memory.</td><td style="word-wrap: break-word;">facebook/esm2_t33_650M_UR50D</td></tr>
<tr><td style="word-wrap: break-word;">batch_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences processed together per inference batch. Valid range is 1 to 1024, default is 64. Increase for better throughput when memory allows; reduce if inference fails due to memory pressure.</td><td style="word-wrap: break-word;">16</td></tr>
<tr><td style="word-wrap: break-word;">device</td><td>True</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">Compute device for inference. Choose `cpu` for broad compatibility or `cuda` when an NVIDIA GPU is available. CUDA is much faster for large checkpoints but requires compatible infrastructure.</td><td style="word-wrap: break-word;">cuda</td></tr>
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
<tr><td style="word-wrap: break-word;">embeddings</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">A DataFrame with columns `sequence_id` and `embedding`. `sequence_id` is taken from each FASTA header, and `embedding` is the pooled ESM-2 vector for that protein sequence. The embedding length depends on the selected checkpoint.</td><td style="word-wrap: break-word;">[{"sequence_id":"spike_RBD_variant_A","embedding":[0.0124,-0.0831,0.2217,0.0046,-0.1379]}, {"sequence_id":"spike_RBD_variant_B","embedding":[0.0189,-0.0794,0.2142,0.0113,-0.1285]}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Larger ESM-2 checkpoints are significantly slower and require more memory. Use smaller checkpoints for iteration, then scale up for final embeddings.
- **Timeout**: The node waits up to 30 minutes for the ESM-2 service to return results. Very large batches, long sequences, or the largest checkpoints may approach this limit.
- **Memory**: `batch_size` directly affects RAM/VRAM usage. If inference fails or stalls on large proteins, lower the batch size before changing the model.
- **Behavior**: FASTA headers are used as output IDs and must be unique. Empty sequences or invalid FASTA content will cause inference to fail.

## Troubleshooting
- **Invalid FASTA**: If the node reports an invalid FASTA error, ensure the input starts with FASTA headers such as `>protein_id` and that every header is followed by a non-empty amino-acid sequence.
- **Duplicate sequence ID detected**: Rename repeated FASTA headers so each record has a unique identifier; the node uses these headers as `sequence_id` values.
- **CUDA requested but not available**: Switch `device` to `cpu` or run the workflow in an environment with a compatible NVIDIA GPU.
- **Out-of-memory or slow inference**: Reduce `batch_size`, choose a smaller checkpoint such as `facebook/esm2_t6_8M_UR50D`, or use CUDA for larger models when available.
