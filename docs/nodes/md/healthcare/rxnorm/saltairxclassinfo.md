# RxClass Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information about a drug class from RxClass using a class ID. Returns a JSON-formatted string with the class data and a status message. Includes input validation and consistent error reporting.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a known RxClass class identifier and need to fetch its details (e.g., to display class metadata or feed downstream classification analysis). Typical workflow: search or determine a class ID, pass it to this node, then parse the returned JSON for class attributes.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass class identifier to query. This is the unique ID used by RxClass to reference a specific drug class.</td><td style="word-wrap: break-word;">100</td></tr>
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
<tr><td style="word-wrap: break-word;">class_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string containing the fetched class information. Structure: {"class_id": "<input>", "data": <rxclass_response>}. On errors, may contain an "error" field in the data.</td><td style="word-wrap: break-word;">{"class_id":"100","data":{...}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing the error.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass info for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input required**: class_id must be a non-empty string; empty input returns an error.
- **Output format**: class_info is a JSON string; parse it before programmatic use.
- **Error reporting**: If the upstream service reports an issue, status will include an "API Error" message and class_info will contain the error details as JSON.
- **Data source**: Results reflect current data available from RxClass and may change over time.

## Troubleshooting
- **Empty input**: If status says "Error: Class ID cannot be empty", provide a valid class_id.
- **Invalid class ID**: If status contains "API Error", verify the class_id against RxClass and try again.
- **Malformed JSON usage**: If downstream nodes fail to parse class_info, ensure you parse the JSON string before accessing fields.
- **Network or rate limits**: Temporary service issues can cause API errors; retry after a short delay.
