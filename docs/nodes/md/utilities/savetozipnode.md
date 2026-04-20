# Save To Zip

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Save To Zip aggregates outputs from many upstream nodes into a temporary folder hierarchy, then compresses everything into a single zip file. It preserves per-node folder names, filenames, seeds, and configs when provided via salt_metadata, and writes plain-text or JSON representations of values. The node returns the local path or URL to the created zip so users can conveniently download all run artifacts in one bundle.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/savetozipnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Save To Zip as the final aggregation node when you want to download all intermediate and final results from a workflow execution in one archive. Common scenarios include exporting multiple PDB, A3M, FASTA, JSON, and text outputs from structural biology or biotech pipelines, saving model configs and seeds alongside produced structures, or packaging debug artifacts from several branches of a workflow. Place Save To Zip near the end of your pipeline and connect any outputs you want to capture to its results_1, results_2, and subsequent results_n inputs; after you connect results_1, additional results_n inputs become visible and can be used for more sources. Internally, each input is assigned to a folder based on its salt_metadata (folder_name, file_name, seed, config) when available, or by the originating node class and output index when metadata is absent. This makes it a natural downstream consumer for nodes like LoadPDBNode, PdbFixerNode, PDBToFastaNode, A3MCombinerNode, and PDBCombinerNode that attach salt_metadata. Connect the output url string from Save To Zip to an Output node or another UI node that exposes clickable links to allow one-click download of the generated zip. For cloud export, you can alternatively use Save To Bucket, which extends this node for GCP buckets but follows the same input patterns and folder organization. As a best practice, configure upstream metadata for clear file and folder naming, and keep the number and size of artifacts reasonable for fast zipping and download.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First data input to be included in the zip. You can connect any output from any node. When the upstream value has salt_metadata (folder_name, file_name, seed, config), that metadata controls where and how the file is written. Without metadata, the node falls back to generic naming based on the source node class and output index, writing a text representation of the value.</td><td style="word-wrap: break-word;">A PDB batch mapping complex identifiers to PDB-formatted strings, with salt_metadata={"folder_name": "esmfold_structures", "file_name": "complex.pdb", "seed": 123, "config": {"model": "esmfold_v1"}}.</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem path or URL-like string to the directory that contains the generated zip file. The zip itself is named as workflow_execution_id combined with an index of this Save To Zip node, and is written under a run-specific folder. Downstream, you typically connect this string to an Output node or similar UI component that converts it into a clickable download link.</td><td style="word-wrap: break-word;">/ComfyUI/output/12345abcd_0/12345abcd_0.zip</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node stages all inputs in a temporary directory and then walks that directory to build the zip. Many large inputs, such as numerous PDB files or long logs, can make it CPU and disk intensive and noticeably slow down workflow completion.
- **Limitations**: Values are stored using JSON when possible, otherwise as plain text via str(). Complex objects that are not JSON-serializable will be stored as their string representation, which may not be directly reloadable by other tools.
- **Behavior**: Inputs with salt_metadata, including folder_name and file_name, are grouped and named according to that metadata. Inputs without metadata are grouped by originating node_class and output_id into .txt files, resulting in generic names if you do not provide metadata upstream.
- **Behavior**: The temporary folder used to stage files is deleted after the zip is created. Only the zip file and its containing folder remain, so intermediate files on disk do not persist outside the archive.
- **Behavior**: The zip name includes both workflow_execution_id and the index of this Save To Zip node in the workflow. Multiple Save To Zip nodes in the same run therefore produce distinct archives even with overlapping inputs.

## Troubleshooting
- **No files in zip or empty archive**: If the created zip is empty, check that you connected outputs to results_1 and any additional results_n inputs, and that upstream nodes returned non-None values. Unconnected or None inputs do not generate files.
- **Unexpected folder or file names**: If files appear under generic folders named after node classes with names such as output_0.txt, the upstream values likely did not include salt_metadata. Update those nodes to attach folder_name and file_name in salt_metadata for clearer structure.
- **Large zip creation is very slow**: If zipping takes a long time or uses significant resources, reduce the number or size of connected artifacts, avoid including very large logs or tensors, or split exports across multiple Save To Zip nodes.
- **Download link not clickable**: If you only see a raw string path in the interface, ensure the url output from Save To Zip is connected to an Output node or another UI node designed to render file links. Save To Zip itself only returns the string value.
- **Missing seeds.json or configs.json**: If seeds.json or configs.json are not present in the archive, confirm that upstream nodes actually set seed and config fields in salt_metadata for at least one input. These summary JSON files are only created when such metadata exists.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/savetozipnode/directory-structure.png" alt="Directory structure" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Directory structure</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/savetozipnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

