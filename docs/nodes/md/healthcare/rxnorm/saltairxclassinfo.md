# RxClass Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information about a drug class from the RxClass service using a class ID. Validates that the class ID is not empty, queries the RxNorm/RxClass API, and returns a formatted JSON string with the results plus a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have a class_id from a prior step (e.g., searching classes by name) to fetch the full metadata for that class. Commonly used in drug classification or analysis workflows to inspect class details before fetching members or related drug information.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier (classId) for the drug class you want information about.</td><td style="word-wrap: break-word;">100</td></tr>
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
<tr><td style="word-wrap: break-word;">class_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string containing the fetched class information, including the supplied class_id and returned data from RxClass.</td><td style="word-wrap: break-word;">{"class_id": "100", "data": { ... }}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message describing success or the nature of any error.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass info for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If class_id is empty, the node returns "{}" and a status indicating the input error.
- On API errors, class_info contains the error payload as JSON and status begins with "API Error: ...".
- On unexpected exceptions, class_info is "{}" and status contains the error message.
- The output JSON is returned as a STRING; downstream nodes may need to parse it if they require structured data.
- Results depend on network availability and the RxNorm/RxClass API; intermittent failures or rate limits may affect output.

## Troubleshooting
- Empty input: Ensure class_id is provided; otherwise you'll receive "{}" with status "Error: Class ID cannot be empty".
- Invalid or unknown class_id: You may receive minimal or empty data; verify the identifier or obtain it via a search node first.
- API Error responses: The status will start with "API Error:". Check the class_info JSON for details and retry later if it's a transient issue.
- Network or connectivity issues: If you receive a generic error status and "{}" output, confirm network access and try again.
