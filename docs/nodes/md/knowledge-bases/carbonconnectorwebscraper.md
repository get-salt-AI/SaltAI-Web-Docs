# Web URLs

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches content from web URLs indexed via the Carbon Web Scraper integration. Given selected Carbon file IDs, it queries the Carbon service and returns a single concatenated string containing the scraped text, segmented per file.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorwebscraper.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to pull text content from one or more web URLs that were previously indexed with the Carbon Web Scraper. Typically placed early in a workflow to gather web-sourced data for downstream tasks like retrieval-augmented generation, summarization, or analysis. Provide the Carbon file IDs (from a file/URL picker) and optionally a query to filter matching chunks on the server side.

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
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">JSON string of Carbon file IDs associated with the Web Scraper integration. These refer to the web URLs whose content should be retrieved.</td><td style="word-wrap: break-word;">["102938", "574839", "998877"]</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional server-side filter to narrow the selection of returned content (e.g., matching keywords within the indexed data).</td><td style="word-wrap: break-word;">error handling best practices</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single concatenated string containing the retrieved content. Each file’s content is wrapped in a simple tag structure, for example: <file id="<carbonId[-chunk:x]>">...</file>.</td><td style="word-wrap: break-word;"><file id="102938"> ...content... </file> <file id="574839-chunk:0"> ...content chunk... </file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration binding**: This node is bound to the Web Scraper integration; ensure the provided file_ids correspond to web URLs indexed by Carbon.
- **Input format**: file_ids must be a valid JSON string (e.g., an array of IDs). Invalid JSON will cause the node to fail.
- **Hidden identity**: salt_user_id is injected by the platform and should not be manually set.
- **Output structure**: The result is a single STRING containing multiple <file> sections, not a list of strings.
- **Filtering**: The optional query input filters results on the server side before returning content.
- **Timeouts**: The request to the Carbon service has a default timeout; large requests or service issues may cause timeouts.

## Troubleshooting
- **Invalid file_ids JSON**: If you see a JSON decoding error, ensure file_ids is a properly formatted JSON string (e.g., ["123", "456"]).
- **Empty output**: If the Output is empty, verify that the provided file IDs exist, belong to the Web Scraper integration, and that your query (if set) is not overly restrictive.
- **Service request failed**: Network or service errors indicate the Carbon service may be unreachable or down. Retry later and verify service health and connectivity.
- **Unexpected content format**: The output is a single concatenated string with <file> wrappers. If a downstream node expects a list, add a parsing step to split by <file> tags.
- **Slow or timed-out requests**: Reduce the number of file IDs, remove overly broad queries, or try again later. Very large datasets can increase response time.
