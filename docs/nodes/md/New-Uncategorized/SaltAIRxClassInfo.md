# RxClass Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information about a specific RxClass using its class ID and returns the results as a JSON string. It validates that the class ID is provided, calls the underlying RxNorm service, and formats the response with the provided class_id and returned data. Status messages indicate success or provide error details if the API reports an issue.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltAIRxClassInfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxClass identifier and need structured metadata about that class (e.g., to display details, verify a class, or feed downstream nodes that require class context). Typical workflow: search or select a class via a search node, pass the class_id here to fetch details, then parse the JSON output for display or further processing.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier to query. Must be a non-empty string representing a valid RxClass class ID.</td><td style="word-wrap: break-word;">100</td></tr>
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
<tr><td style="word-wrap: break-word;">class_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the requested RxClass information, wrapped as {"class_id": ..., "data": ...}.</td><td style="word-wrap: break-word;">{"class_id": "100", "data": {"rxclassMinConceptItem": {"classId": "100", "className": "Example Class", "classType": "ATC"}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message describing the result of the operation or the error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass info for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Inputs must not be empty; an empty class_id returns an empty JSON object and an error status.
- The class_info output is always a JSON string; downstream consumers should parse it before use.
- If the underlying RxNorm service returns an error, the JSON may include an "error" field and the status will begin with "API Error:".
- This node does not validate whether the class_id corresponds to a specific classification system; ensure you provide the correct identifier from your source.
- Network issues or service outages will result in a generic error message and an empty JSON object for class_info.

## Troubleshooting
- Empty class_id: Provide a non-empty RxClass class ID; the node returns "Error: Class ID cannot be empty" if omitted.
- Invalid or unknown class_id: You may receive an API Error status and a payload containing an "error" field; verify the ID from a prior search step.
- Parsing output: If a downstream step cannot read class_info, ensure you parse the JSON string before accessing fields.
- Intermittent API failures: Retry the operation or verify connectivity; check rate limits or service status if repeated API Error statuses occur.
