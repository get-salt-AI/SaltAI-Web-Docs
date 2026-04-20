# Documents

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The CarbonConnectorLocalFiles node lets you use files from a local or mounted filesystem as a Carbon knowledge source. It leverages the shared Carbon data connector behavior (authentication, sync, indexing, and metadata handling) but targets the LOCAL_FILES integration. Use this to bring PDFs, docs, text files, and other local documents into your Carbon-backed search and question‑answering pipelines.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorlocalfiles.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to index and query documents that live on a local disk, network share, or mounted volume rather than a SaaS system. It typically appears early in a workflow where you define what content Carbon should ingest, and is then followed by nodes that control sync/index operations and downstream retrieval (e.g., Carbon ingestion/sync nodes and Carbon query or RAG-style nodes).

Typical workflow position: (1) upstream, you decide on a workspace/project and the folders or patterns you want to expose; (2) you add one or more CarbonConnector* nodes (including this one for LOCAL_FILES) to define data sources; (3) you trigger ingestion and indexing; (4) downstream, you use Carbon-powered search/answer nodes that draw from the indexed content. This node is part of the CarbonConnectors family and behaves similarly to nodes like `CarbonConnectorDropbox`, `CarbonConnectorGmail`, and `CarbonConnectorGitbook`, differing only in that it targets the `LOCAL_FILES` integration.

Common integration patterns include combining this node with other Carbon connectors to unify local and cloud content into a single knowledge base, or using it in on‑prem or privacy‑sensitive deployments where data must stay on local storage. Best practices: clearly scope the folders you expose to avoid indexing unnecessary files, ensure file permissions allow the Salt runtime to read the data, and keep a consistent workspace/project structure so downstream search behavior is predictable.

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
<tr><td style="word-wrap: break-word;">workspace_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier of the Carbon workspace or project into which the local files will be registered. This ties the connector to a particular collection of content and controls which queries will see these documents. Must be an existing workspace ID or one created elsewhere in your Salt/Carbon configuration.</td><td style="word-wrap: break-word;">customer-support-docs-eu-west-1</td></tr>
<tr><td style="word-wrap: break-word;">root_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Absolute or container-visible path to the root directory that should be exposed to Carbon. All files under this path (typically filtered by patterns or types in Carbon configuration) can be considered for ingestion. The path must be reachable from the Salt execution environment and have read permissions.</td><td style="word-wrap: break-word;">/mnt/knowledgebase/support_playbooks</td></tr>
<tr><td style="word-wrap: break-word;">file_patterns</td><td>False</td><td style="word-wrap: break-word;">STRING_LIST</td><td style="word-wrap: break-word;">Optional list of glob-like patterns or extensions that limit which files under the root_path are used. Use this to avoid indexing irrelevant artifacts (e.g., binaries, temporary files). If omitted, the integration’s defaults apply (often common document types such as .pdf, .docx, .txt).</td><td style="word-wrap: break-word;">['**/*.pdf', '**/*.md', '**/*.txt']</td></tr>
<tr><td style="word-wrap: break-word;">recursive</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to scan subdirectories under root_path recursively for documents. When true, the entire directory tree is considered; when false, only the top-level directory is used. Useful for controlling index size and crawl scope.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Optional additional metadata to attach to this connector or its documents (e.g., department, confidentiality level, locale). This metadata can later be used to filter or bias search and retrieval results.</td><td style="word-wrap: break-word;">{"department": "support", "data_classification": "internal", "locale": "en-US"}</td></tr>
<tr><td style="word-wrap: break-word;">sync_mode</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Specifies how Carbon should track and update documents from this local path, such as a full re-sync or incremental updates if supported by your Carbon setup. Valid values depend on your Carbon deployment (e.g., "full", "incremental").</td><td style="word-wrap: break-word;">incremental</td></tr>
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
<tr><td style="word-wrap: break-word;">connector</td><td style="word-wrap: break-word;">CARBON_CONNECTOR</td><td style="word-wrap: break-word;">A Carbon connector descriptor bound to the LOCAL_FILES integration and the configured workspace/path. Downstream Carbon nodes (e.g., ingest/sync or query nodes) consume this object to perform operations like crawling the path, indexing files into the workspace, and serving search/RAG queries against that content.</td><td style="word-wrap: break-word;">{"type": "LOCAL_FILES", "workspace_id": "customer-support-docs-eu-west-1", "config": {"root_path": "/mnt/knowledgebase/support_playbooks", "recursive": true, "file_patterns": ["**/*.pdf", "**/*.md"]}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Indexing very large directory trees or many large files can be slow and resource intensive; constrain root_path and file_patterns to only what you need.
- **Limitations**: The Salt runtime must have direct filesystem access and read permissions for the specified root_path; network-mounted paths must be mounted and stable for the duration of ingestion.
- **Behavior**: This node only defines and configures the LOCAL_FILES data source; actual ingestion, indexing, and querying are handled by downstream Carbon nodes using the returned connector object.
- **Behavior**: Changes to the directory (new, modified, or deleted files) are not automatically reflected until a sync/ingest operation is triggered via the appropriate Carbon workflow nodes.

## Troubleshooting
- **No documents appear in search**: Verify that root_path is correct, accessible from the Salt environment, and that a downstream Carbon ingestion or sync node has been executed after configuring this connector.
- **Permission denied or file access errors**: Ensure the user or container running Salt has read permissions on the entire directory tree under root_path and that any network shares are properly mounted.
- **Too many or irrelevant files indexed**: Narrow the scope by adjusting root_path and setting file_patterns (e.g., restricting to specific extensions) and, if necessary, disabling recursive scanning.
- **Inconsistent results after file changes**: If updated files are not reflected, confirm that the sync_mode is configured as expected and that a new sync/ingest step has been run to propagate changes into Carbon.
