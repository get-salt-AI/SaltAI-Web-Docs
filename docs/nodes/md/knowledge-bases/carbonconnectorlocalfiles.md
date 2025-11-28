# Documents

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects to your local files via Salt's Carbon data integrations and makes selected documents available to downstream nodes. This node is a Carbon connector specialized for local file access.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorlocalfiles.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a data workflow when you want to bring files stored on your machine (local documents) into Salt for processing. It is typically followed by nodes that ingest, index, parse, or analyze the retrieved documents.

## Inputs

No inputs

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
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The collection of documents or document references selected from local files, suitable for downstream ingestion or analysis.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node corresponds to the Carbon integration for local files and appears in the workflow editor as "Documents".
- Access may require that your Carbon connection is configured and authorized for your account or organization.
- The exact document structure and how files are referenced can depend on your environment and Carbon configuration.
- If your environment or organization restricts integrations, ensure the local files integration is enabled.

## Troubleshooting
- No documents are returned: Verify you have an active Carbon session and the local files integration is enabled for your account.
- Cannot see expected files: Check local file permissions and that the files are accessible to the running environment.
- Downstream nodes fail to read the output: Confirm the downstream node supports the document format produced by this connector and any required ingestion steps are included.
