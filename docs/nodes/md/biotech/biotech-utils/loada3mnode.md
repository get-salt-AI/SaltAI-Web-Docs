# Load A3M

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node ingests a raw A3M-formatted multiple sequence alignment string and tags it with a user-defined identifier. It outputs an MSA-typed dictionary where the key is the provided A3M ID and the value is the unchanged A3M string. It serves as a simple loader/adapter to make external A3M files available to Salt biotech workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an A3M multiple sequence alignment (e.g., produced by HHblits, MMseqs2, or external pipelines) and you need to feed it into Salt workflows that expect an MSA input. It typically appears near the start of a biotech pipeline as an input node: you paste or programmatically supply the A3M into `a3m_string` and choose an `a3m_id` that clearly identifies which sequence or chain this MSA corresponds to.

Upstream, the A3M data usually comes from your own file system or an external service, not from another Salt node. Downstream, the output MSA is consumed by nodes that work with MSA dictionaries such as structure prediction or analysis nodes, and can be merged by A3MCombinerNode if you have multiple MSAs. It also complements LoadFastaNode and LoadPDBNode/LoadCIFNode when you build workflows that jointly use sequences, structures, and MSAs.

For best results, keep `a3m_id` consistent with your other IDs (for example, the corresponding FASTA header or chain label) and ensure the A3M content is valid and complete. When working with multiple MSAs, assign unique IDs and later merge them using A3MCombinerNode if required.

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw A3M-formatted multiple sequence alignment text. Must be a non-empty string with one or more A3M records: header lines starting with '>' followed by aligned sequences (typically with insertions in lowercase and deletions as gaps). The node does not enforce size limits, but very large MSAs may affect performance in downstream processing.</td><td style="word-wrap: break-word;">>query_chainA MKTLLILAVALAVFAAEEAQKSTVVKDLGDKVNAVVNNLNNLNNLNNL >homolog_1 MKTLLILAVALAVF-AAEEA-KSTVVKDLGDKVNAaVNNLNNLNNLNNL >homolog_2 MRTLLILAVALAVF-AAEEAQKSTVVKDLGDKVNAaVNNLNNL--LNNL</td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier that will be used as the key for this MSA in the output dictionary. Should be non-empty and unique within a workflow when multiple A3M files are used. For best alignment and traceability, match this ID to the relevant sequence or chain identifier used elsewhere in the workflow.</td><td style="word-wrap: break-word;">chain_A_msa</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">An MSA-typed dictionary containing the loaded A3M alignment. The structure is `{a3m_id: a3m_string}`, where the key is the user-provided ID and the value is the exact A3M content passed in. Downstream nodes that accept MSA inputs can consume this directly, and it can be merged with other MSAs using A3MCombinerNode.</td><td style="word-wrap: break-word;">{'chain_A_msa': '>query_chainA\nMKTLLILAVALAVFAAEEAQKSTVVKDLGDKVNAVVNNLNNLNNLNNL\n>homolog_1\nMKTLLILAVALAVF-AAEEA-KSTVVKDLGDKVNAaVNNLNNLNNLNNL\n>homolog_2\nMRTLLILAVALAVF-AAEEAQKSTVVKDLGDKVNAaVNNLNNL--LNNL'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node only wraps the provided string into a dictionary and adds framework metadata; even large MSAs are cheap to load here, though they may slow down later modeling steps.
- **Limitations**: It does not validate the biological or syntactic correctness of the A3M; malformed or truncated MSAs will still pass through and may cause failures or poor results downstream.
- **Behavior**: The output is always a one-entry dictionary keyed by `a3m_id`. If you reuse the same ID in different branches and then combine them, you can encounter key collisions or unexpected overwrites.
- **Behavior**: Any additional metadata (such as file name or configuration) is managed by the underlying framework; this node itself does not rename, filter, or reformat the A3M content.

## Troubleshooting
- **Common Error 1**: Downstream node reports that an expected MSA is missing or mismatched. Fix by ensuring `a3m_id` matches the expected sequence or chain ID and that the consuming node is wired to the correct msa.a3m output.
- **Common Error 2**: A3MCombinerNode raises a 'At least one A3M input is required' error. This happens when all incoming MSA inputs are empty or missing; confirm that Load A3M is connected and that `a3m_string` is non-empty.
- **Common Error 3**: Downstream parsers complain about unexpected A3M format. Inspect `a3m_string` to ensure each record starts with '>' and that alignment lines contain only valid characters, with no extraneous comments or binary data.
