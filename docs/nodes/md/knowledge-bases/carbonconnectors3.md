# S3

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The CarbonConnectorS3 node binds a Carbon-based data pipeline to an Amazon S3 integration. It relies on the shared Carbon data connector base while specifying that the active integration is S3, enabling Carbon to access, ingest, or synchronize data stored in S3 buckets as part of broader content and knowledge workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectors3.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the S3 connector when your documents, logs, reports, or other files are stored in Amazon S3 and you want Carbon to process them for search, retrieval-augmented generation, analytics, or compliance. In a typical pipeline, this node appears near the start, providing a Carbon data source handle that is then passed into downstream Carbon nodes responsible for extraction, transformation, chunking, embedding, or indexing. Low-level S3 details (buckets, regions, prefixes, credentials) are usually configured once in Carbon’s integration settings, and this node simply selects that integration via the CARBON_INTEGRATIONS.S3 entry. It behaves similarly to other connectors in the CarbonConnectors family (such as CarbonConnectorDropbox, CarbonConnectorBox, or CarbonConnectorGmail) but is specialized for S3-backed data. For best results, centralize S3 credentials and bucket configuration in Carbon’s integration management, keep filters and scopes clearly defined, and treat this node as the reusable entry point to S3 across multiple workflows.

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
<tr><td style="word-wrap: break-word;">filters</td><td>False</td><td style="word-wrap: break-word;">CARBON_FILTERS</td><td style="word-wrap: break-word;">Optional logical filters that restrict which subset of the S3-backed data Carbon should operate on (for example, Carbon-level tags, logical collections, or business domains). The exact format depends on your Carbon deployment and data modeling conventions.</td><td style="word-wrap: break-word;">{"include_tags": ["prod", "docs"], "exclude_tags": ["archive"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">carbon_data_source</td><td style="word-wrap: break-word;">CARBON_DATA_SOURCE</td><td style="word-wrap: break-word;">A Carbon data source handle bound to the S3 integration. Downstream Carbon nodes use this handle to list, sync, and transform data from the configured S3 buckets and prefixes according to the S3 integration’s settings.</td><td style="word-wrap: break-word;">{"source_id": "src_s3_main_docs", "integration": "s3", "status": "ready", "metadata": {"description": "Primary product documentation bucket", "default_prefix": "public/docs/"}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Runtime is influenced by S3 listing and object retrieval plus Carbon’s processing; very large buckets or many small files can significantly increase execution time and may benefit from narrower prefixes or incremental syncs.
- **Limitations**: Bucket names, regions, prefixes, IAM roles, and credentials are not configured on this node; they must be defined and maintained in the Carbon S3 integration referenced by CARBON_INTEGRATIONS.S3.
- **Behavior**: This node’s primary function is to choose the S3 integration via getIntegration; details like sync frequency, deduplication, and conflict handling are governed by the shared BaseCarbonDataNode logic and Carbon backend configuration.
- **Behavior**: If the S3 integration is missing, disabled, or misconfigured, workflows using this node will typically fail when they first attempt to access S3, even though the node appears correctly wired in the pipeline.

## Troubleshooting
- **Common Error 1**: "S3 integration not configured" – Confirm that an S3 integration exists and is enabled in Carbon’s integration settings for the current workspace, and that this workspace is allowed to use it.
- **Common Error 2**: S3 permission errors (for example, "AccessDenied" or "Forbidden") – Verify the IAM role or access keys configured in the Carbon S3 integration have list and read permissions for the relevant buckets and prefixes.
- **Common Error 3**: No or too few documents appear downstream – Check that the configured S3 bucket and prefix contain supported file types, and review any Carbon-level filters or scopes that may be excluding objects unintentionally.
- **Common Error 4**: Timeouts or intermittent failures on large buckets – Narrow the integration scope using prefixes or collection filters, or rely on incremental sync strategies in Carbon to avoid long-running full-bucket scans.
