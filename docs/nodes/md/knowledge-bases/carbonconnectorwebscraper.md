# Web URLs

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches content from web pages connected via the Carbon Web Scraper integration and returns them as a single formatted string. Each selected file (or chunk) is wrapped in a lightweight file tag so downstream nodes can parse or prompt with clear boundaries.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorwebscraper.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to bring web page content you've connected through Carbon's Web Scraper into your workflow. Typically, you first connect URLs using the Carbon connection modal, pick the files with the file picker, optionally provide a query to narrow results, and then pass the combined output to processing or LLM nodes.

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
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">Identifiers of the Carbon files (web pages or scraped chunks) to fetch. This should be supplied by the Carbon file picker for the Web Scraper integration.</td><td style="word-wrap: break-word;">[101, 202, 303]</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter to narrow the selection of content returned from the chosen files (for example, keywords or a brief phrase).</td><td style="word-wrap: break-word;">product pricing policy</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single concatenated string containing all fetched file contents, where each file is wrapped as: <file id="<id[-chunk:idx]>"> ... </file>.</td><td style="word-wrap: break-word;"><file id="101"> <page content here> </file> <file id="202-chunk:0"> <chunk content here> </file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration scope**: This node specifically targets the Carbon Web Scraper integration; ensure you have connected web URLs via Carbon before use.
- **File selection**: Use the Carbon file picker to populate file_ids. Manually crafting file_ids must result in a valid JSON array string.
- **Output format**: Results are combined into one STRING with <file> tags; some IDs may include a -chunk:index suffix when content is chunked.
- **Filtering**: The optional query input helps narrow returned content but does not replace selecting the correct files/URLs.
- **Permissions**: Access is restricted to the authenticated Salt user; content fetches respect your Carbon connection permissions.

## Troubleshooting
- **Invalid file_ids (JSON error)**: Ensure file_ids is a valid JSON array string (e.g., "[101, 202]") and that it comes from the Carbon file picker.
- **No content returned**: Verify the selected URLs are connected in Carbon, you have permission to access them, and the query isn't overly restrictive.
- **Wrong integration**: If you don't see your web URLs in the picker, confirm you're using the Web Scraper integration and that the connection is active.
- **Downstream length limits**: If subsequent nodes fail due to large input, reduce the number of selected files or apply a more specific query.
