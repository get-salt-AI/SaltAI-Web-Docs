# RxClass Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information about a specific RxClass given its class identifier. Returns a JSON-formatted string with the class ID and the full data payload from the RxClass service, along with a status message. Handles empty inputs and API errors with clear status feedback.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you know an RxClass identifier and need its metadata (e.g., class name, type, related attributes). Typical workflow: search or derive a class ID using a search node, then pass that ID here to fetch authoritative details for reporting or downstream processing.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier (classId) to look up. This may be a numeric or alphanumeric ID, depending on the classification source.</td><td style="word-wrap: break-word;">D007398</td></tr>
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
<tr><td style="word-wrap: break-word;">class_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the requested class information. Structure: {"class_id": "<input>", "data": { ...raw RxClass data... }}</td><td style="word-wrap: break-word;">{"class_id":"D007398","data":{...}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or providing error details.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass info for class ID D007398</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: If class_id is empty or whitespace, the node returns "{}" for class_info and an error status.
- **Error propagation**: If the upstream RxClass service returns an error, the error is included in the output JSON and reflected in the status as an API Error.
- **Output format**: class_info is a JSON string. Downstream nodes expecting structured data should parse this string.
- **Identifier format**: class_id formats vary by source (e.g., numeric or codes like D007398). Ensure you supply a valid RxClass classId.
- **No source selector**: This node looks up class info by ID only; it does not take a classification system parameter.

## Troubleshooting
- **Empty class_id**: Ensure the class_id field is not blank. Provide a valid RxClass identifier.
- **Invalid or unknown class_id**: Verify the ID exists. Try locating the class via a search node and use the returned classId.
- **API Error in status**: The external service may be unavailable or the ID invalid. Retry later or validate the identifier.
- **Unexpected output structure**: Remember class_info is a JSON string. Parse it before attempting to access fields.
- **Rate limiting or network issues**: If intermittent failures occur, implement retries or reduce request frequency.
