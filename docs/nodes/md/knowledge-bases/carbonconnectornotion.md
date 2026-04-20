# Notion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node connects to the Carbon Notion integration and retrieves relevant content from previously indexed Notion pages or databases. It accepts a natural-language query and a set of Carbon file IDs that identify specific Notion resources, then returns the matching data as a serialized payload for downstream processing. It is a dedicated Carbon data connector for the NOTION integration type.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectornotion.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when your workflow needs to search or pull context from Notion content that has already been ingested into Carbon. Common scenarios include answering questions over team documentation, enriching model prompts with product specs or runbooks stored in Notion, and building assistants that can reference specific Notion pages or databases. Place this node after any configuration or selection step that determines which Notion resources to use (for example, a node that stores or selects Carbon file IDs), and before nodes that perform reasoning, summarization, or response generation over the retrieved content. It fits into the broader Carbon connector family and can be combined with other connectors (such as Carbon connectors for different tools) to aggregate context from multiple systems. For best results, keep the `query` specific and pass only the Notion file IDs relevant to the current task so that retrieval is faster and more focused.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural-language query that instructs Carbon what to retrieve from the selected Notion content. This is used to search within the specified Notion pages or databases that have been indexed into Carbon. Queries should clearly describe the information or passages you are looking for; highly vague or extremely long queries may reduce retrieval precision.</td><td style="word-wrap: break-word;">List the key milestones and deadlines from the "Q3 Launch Plan" Notion page.</td></tr>
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">Collection of Carbon file IDs that scope the query to particular Notion pages, databases, or spaces. Each ID must correspond to Notion content already ingested by Carbon using the NOTION integration. Passing a very broad set of IDs can increase latency and noise, while invalid or empty sets will lead to no useful results.</td><td style="word-wrap: break-word;">["carbon_notion_file_12ab34cd", "carbon_notion_file_98fe76dc"]</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Serialized result payload from Carbon for the NOTION integration, most commonly a JSON-formatted string. It usually includes retrieved Notion snippets, associated metadata (titles, URLs, file IDs), and relevance scores. Downstream nodes should parse this string if they need to work with structured elements such as document sections or links.</td><td style="word-wrap: break-word;">{ "results": [ { "file_id": "carbon_notion_file_12ab34cd", "title": "Q3 Launch Plan", "url": "https://www.notion.so/workspace/Q3-Launch-Plan-...", "snippet": "The launch is scheduled for September 15 with a beta in August...", "score": 0.91 } ], "integration": "NOTION" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Restrict `file_ids` to the smallest relevant set of Notion pages or databases to improve latency and reduce irrelevant matches.
- **Limitations**: The node only works with Notion content that has been previously ingested and indexed by Carbon; recent edits or new pages may not appear until indexing has updated.
- **Behavior**: The integration type is fixed to NOTION through the node’s implementation; switching to another system requires using a different Carbon connector node.
- **Behavior**: Output is returned as a single string payload; workflows that need specific fields must include a parsing or transformation step after this node.

## Troubleshooting
- **Empty or missing results**: If the output string contains no or very few `results`, verify that the provided `file_ids` are valid Carbon Notion file IDs and that the underlying Notion pages were successfully ingested. Also try simplifying or broadening the `query`.
- **Access or permission-related errors**: Errors mentioning user context or authorization may indicate issues with the `salt_user_id` or workspace configuration. Ensure the execution context corresponds to a valid Salt user with access to the relevant Carbon integration.
- **Irrelevant content returned**: When results do not match the user’s intent, reduce the number of `file_ids` to only the most relevant Notion pages and rephrase the `query` to be more concrete about what information is needed.
- **Unexpected format in downstream nodes**: If downstream nodes fail when handling the output, confirm they expect a STRING/JSON payload and add a parsing or mapping step to convert the Carbon response into the structure those nodes require.
