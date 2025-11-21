# RxClass Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed RxClass information for a given RxClass class ID using the RxNorm/RxClass service. Returns the raw data as a JSON string along with a status message. The output is formatted with the requested class_id and the service response for easier downstream handling.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an RxClass class identifier and need to fetch its metadata/details (e.g., for display, validation, or as input to further Rx-related nodes). Typical workflow: search or determine a class ID via a search node or external source, then pass that class_id here to obtain structured information about the class.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier to look up. This should be a valid class ID from the RxClass system.</td><td style="word-wrap: break-word;">100</td></tr>
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
<tr><td style="word-wrap: break-word;">class_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the fetched RxClass information, formatted as {"class_id": "<input>", "data": <service_response>}.</td><td style="word-wrap: break-word;">{"class_id": "100", "data": {"rxclassMinConceptItem": {"classId": "100", "className": "Example", "classType": "ATC"}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or describing the error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass info for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Required input**: class_id must be a non-empty string; empty input returns an error status and empty JSON.
- **Output format**: class_info is a JSON string that includes the original class_id and a 'data' object containing the RxClass API response.
- **Error handling**: If the underlying service returns an error, the 'data' may include an 'error' field and the status will describe the API error.
- **Dependency**: Relies on the RxNorm/RxClass service response; availability and content depend on that external service.
- **No schema guarantee**: The structure within 'data' reflects the RxClass response and may vary by class or service version.

## Troubleshooting
- **Empty class_id**: Ensure 'class_id' is provided; empty input results in status 'Error: Class ID cannot be empty' and '{}' for class_info.
- **API Error in output**: If status starts with 'API Error:' and class_info contains an 'error' field, verify the class_id is valid and retry later in case of service issues.
- **Unexpected data shape**: If downstream parsing fails, inspect the class_info JSON to accommodate the exact structure returned by RxClass for that class.
- **No results found**: Some class IDs may return minimal or empty data; confirm the identifier from a trusted source or use a search node to locate the correct ID.
