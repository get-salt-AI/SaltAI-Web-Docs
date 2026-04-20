# Google Drive

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

CarbonConnectorGoogleDrive is a Carbon data connector node that tells the Carbon backend to use the Google Drive integration. It inherits its behavior from a shared Carbon base node and only specifies that the underlying integration is Google Drive. Use this node whenever you want Carbon-based knowledge or retrieval flows to pull content from Google Drive.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgoogledrive.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to build a knowledge or retrieval pipeline that searches and reasons over documents stored in Google Drive. Typically, this node appears near the start of a knowledge workflow: you configure the Carbon integration (credentials, scopes, workspace, etc.) in your Salt/Carbon settings, then place CarbonConnectorGoogleDrive to bind that integration into the pipeline. Downstream nodes can then run indexing, semantic search, or question-answering over the Drive-backed knowledge base.

A common pattern is: (1) select or configure a Carbon knowledge base and workspace, (2) use CarbonConnectorGoogleDrive to attach Google Drive as the data source, and (3) feed the resulting Carbon data into search or LLM nodes (for example, a Carbon search/query node followed by a chat or summarization node). This node is part of a family of Carbon connectors (e.g., CarbonConnectorDropbox, CarbonConnectorGitHub, CarbonConnectorLocalFiles) and behaves consistently with them; you can swap connectors to change the source system while keeping the rest of the workflow the same. Before using it, ensure the Google Drive integration is authorized and has the correct Drive access scopes configured in Carbon.

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
<tr><td style="word-wrap: break-word;">knowledge_base_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier of the Carbon knowledge base that will be populated from, or queried against, Google Drive content. Must refer to an existing knowledge base configured in your Salt/Carbon environment.</td><td style="word-wrap: break-word;">kb_customer_support_drive_docs</td></tr>
<tr><td style="word-wrap: break-word;">workspace_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Workspace or tenant identifier that determines which Carbon workspace and integration configuration to use. This must match the workspace where the Google Drive integration is connected.</td><td style="word-wrap: break-word;">workspace_acme_corp_prod</td></tr>
<tr><td style="word-wrap: break-word;">sync_mode</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional mode that controls how Carbon pulls data from Google Drive, such as performing a full reindex or only fetching updates since the last run. The exact accepted values depend on your Carbon backend configuration.</td><td style="word-wrap: break-word;">incremental</td></tr>
<tr><td style="word-wrap: break-word;">folder_filters</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter for restricting which Google Drive folders are included in the connector. Use to limit indexing to a subset of folders by ID, path, or a pattern understood by your Carbon deployment.</td><td style="word-wrap: break-word;">['1A2B3CProjectFolderId', '9X8Y7ZSharedDriveFolderId']</td></tr>
<tr><td style="word-wrap: break-word;">file_types</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional list or expression specifying which file types to index from Google Drive (such as PDFs, Google Docs, or text files). When omitted, Carbon may use its default set of supported types.</td><td style="word-wrap: break-word;">['application/pdf', 'application/vnd.google-apps.document']</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">If used in a query-type flow, this is the natural-language or keyword query that Carbon should execute against the knowledge base populated from Google Drive. Use this when the node is part of a retrieval path rather than a pure sync.</td><td style="word-wrap: break-word;">Onboarding checklist for new enterprise customers</td></tr>
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
<tr><td style="word-wrap: break-word;">carbon_data</td><td style="word-wrap: break-word;">CARBON_DATA</td><td style="word-wrap: break-word;">Structured Carbon data associated with the Google Drive integration. In sync scenarios, it represents the Drive-derived documents and metadata now available in the knowledge base; in query scenarios, it contains matched documents, snippets, and metadata drawn from Google Drive content, ready for downstream ranking or LLM processing.</td><td style="word-wrap: break-word;">{ "source": "google_drive", "knowledge_base_id": "kb_customer_support_drive_docs", "documents": [{ "id": "drive-file-123", "title": "Enterprise Onboarding Checklist", "mime_type": "application/vnd.google-apps.document", "snippet": "This checklist covers the steps required for onboarding new enterprise customers...", "uri": "https://drive.google.com/open?id=drive-file-123" }], "query": "Onboarding checklist for new enterprise customers" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Indexing large or highly nested Google Drive accounts can be slow and resource-intensive; use folder_filters and file_types to focus on the most relevant content.
- **Limitations**: The connector only accesses files that the underlying Google Drive integration account can see; private or restricted files outside that account's permissions will not be indexed or searchable.
- **Behavior**: This node itself only declares that the GOOGLE_DRIVE integration should be used; all API calls, retries, and rate limiting are handled by the Carbon integration layer behind the scenes.
- **Behavior**: Updates in Google Drive may not appear immediately in search results; depending on your Carbon settings, you may need to trigger an explicit sync or rely on scheduled background syncs.

## Troubleshooting
- **No Drive documents appearing in search**: Confirm that the Google Drive integration is connected and authorized in Carbon for the specified workspace_id, that the correct knowledge_base_id is used, and that a sync has completed without errors.
- **Expected files missing from the knowledge base**: Check folder_filters and file_types for overly narrow filters, and verify that the integration account has at least read access to the missing files or shared drives.
- **Permission or auth-related backend errors**: If logs show authentication or insufficient-permission errors, reauthorize the Google Drive integration, ensuring proper OAuth scopes (typically read access to Drive files) and valid credentials.
- **Slow workflows when indexing**: For very large Drives or frequent full syncs, workflows may time out or run slowly; narrow the scope using folder_filters, schedule heavy syncs during off-peak times, or choose an incremental sync_mode if supported.
