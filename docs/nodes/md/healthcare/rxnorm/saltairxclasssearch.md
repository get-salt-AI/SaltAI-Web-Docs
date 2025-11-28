# RxClass Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the RxClass database for drug classes by name and returns a JSON-formatted result and a status message. It validates the input, handles API errors, and formats results to include the original search term with the returned data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclasssearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to find drug classes by name (e.g., "ACE Inhibitors") to explore their identifiers or related metadata. It typically precedes nodes that require a class ID, such as fetching class information or listing class members.

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
<tr><td style="word-wrap: break-word;">class_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug class name to search for in RxClass.</td><td style="word-wrap: break-word;">ACE Inhibitors</td></tr>
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
<tr><td style="word-wrap: break-word;">class_search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the search term and the RxClass search results.</td><td style="word-wrap: break-word;">{"search_term": "ACE Inhibitors", "results": {"rxclassMinConceptList": {"rxclassMinConcept": [{"classId": "...", "className": "Angiotensin-Converting Enzyme Inhibitors", "classType": "..."}]}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully searched RxClass for 'ACE Inhibitors'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input required**: An empty class name returns an empty JSON object ("{}") and an error status.
- **Output format**: The first output is a JSON-formatted string, not a parsed object. Downstream nodes expecting structured data must parse it.
- **API behavior**: Results are sourced from the RxClass service; availability, rate limits, and content depend on the external API.
- **Error handling**: If the API reports an error, the error details are returned within the JSON string and reflected in the status message.
- **Typical workflow**: Use the returned class identifiers from the results to query class details or members in subsequent nodes.

## Troubleshooting
- **No results or empty list**: Verify the spelling of the class name or try a broader term (e.g., search for "Statin" instead of a brand-specific term).
- **Status shows 'Class name cannot be empty'**: Provide a non-empty class_name value.
- **API Error in status**: Indicates a network or service issue. Check connectivity, try again later, or reduce request frequency.
- **Downstream parsing errors**: Ensure you parse the JSON string from class_search_results before accessing fields.
- **Unexpected fields or structure**: The API response format may change; inspect the raw JSON output to adapt downstream processing.
