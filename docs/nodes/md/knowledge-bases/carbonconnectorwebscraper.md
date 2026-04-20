# Web URLs

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The CarbonConnectorWebScraper node connects your Salt pipeline to Carbon’s Web Scraper integration so you can ingest and synchronize content from public web URLs. It delegates all data fetching, parsing, and transformation to the Carbon backend, exposing a uniform Carbon data output like other Carbon connectors. Use it when you want to build knowledge bases or search over live website content alongside other document sources.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorwebscraper.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to bring external website content (e.g., docs sites, blogs, support portals) into Carbon as a data source. It typically appears early in the workflow, where upstream nodes provide configuration (such as credentials, project IDs, or integration configuration if applicable), and downstream nodes consume the resulting Carbon data for search, retrieval-augmented generation, or analytics.

In a common pipeline, you might first configure Carbon connection details (using a generic Carbon authentication/credentials node, if available in your environment), then add `Web URLs` to specify that the integration is the Web Scraper. Downstream, you would chain into nodes like a Carbon query node (for semantic search / vector retrieval), a reranker node, or an LLM node that uses the scraped content for context.

This node behaves like all other `CarbonConnector*` nodes (e.g., `CarbonConnectorLocalFiles`, `CarbonConnectorGitHub`, `CarbonConnectorDropbox`), but is specialized for website URLs via `CARBON_INTEGRATIONS.WEB_SCRAPER`. Follow the same patterns you use with other Carbon connectors: connect credentials and configuration upstream, then feed its output into retrieval or QA nodes. For best results, limit URLs to the domains you control, avoid pages that need authentication unless configured in Carbon, and keep URL lists to a practical size to avoid long ingestion times.

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
<tr><td style="word-wrap: break-word;">carbon_config</td><td>True</td><td style="word-wrap: break-word;">CARBON_CONFIG</td><td style="word-wrap: break-word;">Shared configuration for Carbon connectors, typically including connection/tenant settings, workspace or project identifiers, and any authentication required to talk to the Carbon service. This is usually produced by a dedicated Carbon configuration or credentials node and reused across multiple CarbonConnector nodes.</td><td style="word-wrap: break-word;">{"tenant_id": "acme", "project_id": "web-knowledge-base", "api_endpoint": "https://carbon.<region>.example/api", "auth_token": "<carbon-api-token>"}</td></tr>
<tr><td style="word-wrap: break-word;">source_definition</td><td>True</td><td style="word-wrap: break-word;">CARBON_SOURCE_DEFINITION</td><td style="word-wrap: break-word;">Definition of the source to ingest into Carbon using the Web Scraper integration. For web URLs this typically includes one or more URLs, optional crawl depth, inclusion/exclusion patterns, and scheduling or update behavior. Exact shape is defined by the Carbon backend for WEB_SCRAPER but follows the same pattern as other Carbon source definitions.</td><td style="word-wrap: break-word;">{"integration": "WEB_SCRAPER", "name": "Product Docs Website", "urls": ["https://docs.acme.com/", "https://blog.acme.com/product-updates"], "max_depth": 2, "include_patterns": ["https://docs.acme.com/**"], "exclude_patterns": ["https://docs.acme.com/admin/**"], "refresh_interval_hours": 24}</td></tr>
<tr><td style="word-wrap: break-word;">ingestion_options</td><td>False</td><td style="word-wrap: break-word;">CARBON_INGESTION_OPTIONS</td><td style="word-wrap: break-word;">Optional fine‑tuning parameters for ingestion behavior such as whether to perform a full re-crawl or incremental sync, how to handle deletes or redirects, and any per-run overrides of the default Web Scraper configuration. If omitted, Carbon uses its integration defaults for WEB_SCRAPER.</td><td style="word-wrap: break-word;">{"mode": "incremental", "force_full_reindex": false, "timeout_seconds": 900, "max_pages": 500}</td></tr>
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
<tr><td style="word-wrap: break-word;">carbon_source</td><td style="word-wrap: break-word;">CARBON_SOURCE</td><td style="word-wrap: break-word;">A Carbon source object representing the configured Web Scraper source (Web URLs) inside Carbon. Downstream nodes can use this to trigger ingestion runs, monitor status, or query scraped content alongside other Carbon sources. The structure is the standard Carbon source descriptor shared by all CarbonConnector nodes.</td><td style="word-wrap: break-word;">{"id": "src_web_scraper_01HXTZ3JM8Y5K7ZQ3ZMDJ0D7PQ", "integration": "WEB_SCRAPER", "name": "Product Docs Website", "status": "active", "config": {"urls": ["https://docs.acme.com/"], "max_depth": 2}, "created_at": "2026-04-20T09:15:23Z", "updated_at": "2026-04-20T09:16:10Z"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large sites or high crawl depths can significantly increase ingestion time and load; limit depth and page count where possible.
- **Limitations**: The Web Scraper integration can only access publicly reachable pages (or pages supported by your Carbon configuration); pages behind complex authentication or heavy client-side rendering may not be fully captured.
- **Behavior**: This node only selects the WEB_SCRAPER integration via Carbon; the actual scraping, parsing, and deduplication happen asynchronously in Carbon’s backend and may complete after the node returns.
- **Behavior**: Configuration patterns (fields, options) are shared with other Carbon connectors; if you clone configurations from nodes like `CarbonConnectorLocalFiles` or `CarbonConnectorGitHub`, ensure that WEB_SCRAPER-specific fields (e.g., URLs and crawl depth) are correctly set.

## Troubleshooting
- **Web content not appearing in queries**: Ensure the `carbon_source` produced by this node is connected to a downstream Carbon query or indexing node, and verify in Carbon that the WEB_SCRAPER source has completed at least one successful ingestion run.
- **Ingestion takes too long or times out**: Reduce `max_depth` or limit the number of URLs in your `source_definition`. Also verify your `ingestion_options.timeout_seconds` is appropriate for the size of the site.
- **Unexpected or missing pages**: Check `include_patterns` and `exclude_patterns` in your source definition. Overly broad excludes or missing includes often cause pages to be skipped by the Web Scraper integration.
