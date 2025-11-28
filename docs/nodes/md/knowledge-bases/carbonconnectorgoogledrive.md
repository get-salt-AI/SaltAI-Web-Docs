# Google Drive

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects your Salt workspace to a Google Drive account via Carbon integrations. It authorizes access to Drive, discovers eligible files/folders, and prepares them for downstream processing or ingestion steps. The integration type is fixed to Google Drive and all common connector behaviors (inputs/outputs) are inherited from the Carbon data connector base.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgoogledrive.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a data workflow when your source documents live in Google Drive. Typically, you will authenticate the connection (using a Carbon-scoped token), optionally scope which folders/files to include, and pass the resulting handle/output to nodes that fetch, index, or process file contents.

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
<tr><td style="word-wrap: break-word;">authentication</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Credentials or token used to authorize access through the Carbon service to your Google Drive data. Typically obtained via a prior authorization step.</td><td style="word-wrap: break-word;"><carbon-access-token></td></tr>
<tr><td style="word-wrap: break-word;">selection</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional scoping to limit which files or folders from Google Drive are included (e.g., folder IDs, file filters).</td><td style="word-wrap: break-word;">{"folderIds": ["12345abcde"], "includeShared": true}</td></tr>
<tr><td style="word-wrap: break-word;">sync_options</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional synchronization or filtering options (e.g., modified-since time, file types).</td><td style="word-wrap: break-word;">{"modifiedAfter": "2024-01-01T00:00:00Z", "mimeTypes": ["application/pdf", "application/vnd.google-apps.document"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A handle or reference to the selected Google Drive data within Carbon, to be consumed by downstream nodes that fetch or process file content.</td><td style="word-wrap: break-word;">{"provider": "google_drive", "items": [{"id": "1AbCd...", "name": "Report.pdf"}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node’s integration is fixed to Google Drive through the Carbon connector system.
- Authentication must be scoped correctly to allow access to the target Drive files and any shared drives you intend to include.
- Large folders or many shared drives can increase discovery time; consider filtering by folders or mime types.
- Actual file content retrieval or indexing is typically performed by downstream nodes; this node prepares the source reference.

## Troubleshooting
- Authorization failed or expired: Regenerate the Carbon-scoped token and re-run.
- No files returned: Verify folder IDs, filters, and that the authorized account has access (including shared drives).
- Partial results: Check rate limits or reduce scope with filters; then re-run to iterate.
- Unexpected file types: Add or adjust mime type filters in the selection or sync options to include the formats you need.
