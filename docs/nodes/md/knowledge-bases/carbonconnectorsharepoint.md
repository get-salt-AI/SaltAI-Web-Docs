# Sharepoint

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects a SharePoint account or site to Salt’s Carbon data layer. This node acts as a SharePoint-specific selector on top of the shared Carbon data-connector base, enabling authenticated access to SharePoint document libraries and lists for syncing and downstream use in workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorsharepoint.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to ingest or reference content stored in SharePoint (e.g., document libraries, team sites) inside a Salt workflow. Typically, you first establish and authorize a SharePoint connection in the Carbon UI, then drop this node into your pipeline to point at the desired SharePoint site or content scope, and finally route the resulting dataset or file references to nodes that parse, embed, or analyze the content.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Configuration for this connector is provided by the shared Carbon data connector base for the SharePoint integration. This usually includes which SharePoint connection to use, site or library selection, and optional sync or filtering parameters, all managed through the Carbon connection/configuration interface rather than direct node fields.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">connection</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A configured Carbon data connector instance bound to the SharePoint integration. Downstream nodes use this output to access SharePoint-hosted documents and metadata without reconfiguring authentication or connection details.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Connector role**: This node primarily selects and configures the SharePoint integration; the heavy lifting for auth, sync, and retrieval is handled by the shared Carbon data connector base.
- **Configuration surface**: Most options (e.g., which SharePoint account, sites, or libraries to include) are chosen in the Carbon connection UI, not as explicit per-field settings on the node.
- **Read vs write**: Behavior is generally oriented around reading and syncing content from SharePoint for use in search, RAG, or analytics. Any write-back behavior, if supported, depends on the Carbon SharePoint integration configuration.
- **Integration dependency**: The node requires that the SharePoint Carbon integration be available and properly configured in your Salt environment; otherwise it will not be able to authenticate or list content.

## Troubleshooting
- **No SharePoint data appears downstream**: Confirm that your SharePoint connection is authorized and active in the Carbon integrations area, and that the node is pointing at the correct connection/site or library.
- **Authentication or permission errors**: Check that the SharePoint user or app used for the Carbon connection has sufficient permissions to the selected sites and libraries, then reauthorize the connection if scopes have changed.
- **Unexpectedly empty or partial results**: Review any filters or scope selections (such as site, document library, or folder restrictions) in the Carbon SharePoint connection configuration, and ensure they match the content you expect to sync.
- **Node fails to run after connection changes**: If you recently modified or rotated credentials in SharePoint or in the Carbon integration, refresh or re-save the SharePoint connection in Carbon and then re-run the workflow so the node picks up the updated configuration.
