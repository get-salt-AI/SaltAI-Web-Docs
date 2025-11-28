# Web URLs

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon connector configured for the Web Scraper integration. It ingests content from one or more web URLs using the standard Carbon data-connector flow provided by the base Carbon node. Behavior (inputs/outputs, sync, and error handling) is inherited from the generic Carbon data node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorwebscraper.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to ingest public web pages into your Carbon data workspace for downstream search, RAG, or analytics. Place it at the beginning of a data ingestion workflow, configure the standard Carbon connector settings (as exposed by the base Carbon data node), and connect its output to nodes that index, process, or consume the ingested content.

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
<tr><td style="word-wrap: break-word;">urls</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">One or more web URLs to scrape and ingest. Exact format and multiplicity are defined by the base Carbon data node.</td><td style="word-wrap: break-word;">https://example.com, https://example.com/docs/*</td></tr>
<tr><td style="word-wrap: break-word;">connector_authentication</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Standard Carbon connector credentials or token input as defined by the base Carbon data node.</td><td style="word-wrap: break-word;"><carbon-access-token></td></tr>
<tr><td style="word-wrap: break-word;">ingestion_options</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Common ingestion settings (e.g., crawl depth, include/exclude patterns, scheduling) exposed by the base Carbon data node, if available.</td><td style="word-wrap: break-word;">Not specified</td></tr>
<tr><td style="word-wrap: break-word;">destination_dataset</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Target dataset or collection within Carbon where scraped content will be stored, as defined by the base Carbon data node.</td><td style="word-wrap: break-word;">web_knowledge_base</td></tr>
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
<tr><td style="word-wrap: break-word;">connector_output</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The standard output produced by Carbon data connector nodes (e.g., a handle or reference to the created/updated dataset or sync job). Exact type and structure are defined by the base Carbon data node.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is a thin wrapper that selects the Web Scraper integration. All core behavior (inputs, outputs, validation, and execution) is defined by the base Carbon data node.
- The display name for this node is "Web URLs".
- Any required authentication, workspace, or dataset configuration must be provided via the standard inputs exposed by the base Carbon data node.
- The exact input and output types are not specified in this file and may vary with the version of the base Carbon data node.

## Troubleshooting
- If inputs are unclear or missing, refer to the base Carbon data node documentation for the full list of required/optional parameters.
- If ingestion fails, verify that the URLs are accessible and valid, and check that your Carbon credentials (if required) are correctly configured.
- If no data appears downstream, ensure the destination dataset/workspace exists and that the sync job completed successfully as reported by the base Carbon data node.
