# Web Research Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The Web Research Agent node sends a configured research task to an external GPT-based service that can use web search, specific URLs, and local documents. It builds a research query plus report instructions based on your chosen report type, format, and models, then returns a synthesized report and a list of references. This lets you centralize complex research behavior into a single reusable node in your workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/webresearchagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need thorough research with citations, not just a single LLM response. Typical scenarios include competitive or market analysis, technology surveys, regulatory overviews, or summarizing curated URLs and internal documents into structured reports. Place it after nodes that gather requirements or user input (for example, form/JSON parsers or prompt planners that generate `query` and `task_context`), and before nodes that transform or distribute results (such as report-to-PDF generators, email composers, or dashboard updaters). It works well with upstream logic that selects `report_type` and `report_format` based on user choice, and with downstream nodes that parse `references` to build citation lists or fact-check flows. For best results, keep the `query` focused, use `task_context` to describe audience and constraints, add `source_urls` to constrain research to trusted sites, and set `local_documents_path` when your private files must be part of the research evidence.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The primary research question or topic in natural language. Can be multi-line and may include sub-questions and constraints; overly vague queries may result in generic answers, so include scope, timeframe, and focus areas when possible.</td><td style="word-wrap: break-word;">Research the current landscape of vector database providers for AI retrieval applications in 2025, focusing on features, scalability, data residency options, and typical pricing models.</td></tr>
<tr><td style="word-wrap: break-word;">report_type</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Conceptual type of report to generate, drawn from a predefined `report_types` set (for example, overview, competitive_analysis, step_by_step_guide). This choice influences how the backend plans, structures, and emphasizes different parts of the research.</td><td style="word-wrap: break-word;">competitive_analysis</td></tr>
<tr><td style="word-wrap: break-word;">report_format</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Desired output formatting style, selected from predefined `report_formats` (such as markdown_report, bullet_points, executive_summary). This controls sectioning, level of detail, and how the final report is formatted.</td><td style="word-wrap: break-word;">markdown_report</td></tr>
<tr><td style="word-wrap: break-word;">task_context</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional additional context about the task, audience, domain, or constraints. When non-empty, it is injected into the query using an internal template so the research and writing are tailored to your specific scenario.</td><td style="word-wrap: break-word;">You are preparing an internal briefing for the CTO of a mid-sized European fintech company. Emphasize EU data residency, compliance, and long-term vendor stability over experimental features.</td></tr>
<tr><td style="word-wrap: break-word;">embed_model</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Embedding model used for semantic search and context retrieval within the research pipeline. Must be one of the models returned by `get_embed_models()` and is passed to the backend as `openai:<model_name>`. Smaller models are faster and cheaper; larger models can improve relevance.</td><td style="word-wrap: break-word;">text-embedding-3-small</td></tr>
<tr><td style="word-wrap: break-word;">fast_llm_model</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">LLM model used for fast, lower-cost steps in the research pipeline such as quick summaries or intermediate transformations. Selected from `get_llm_models()` and sent as `openai:<model_name>`. Choose a cost-efficient model if you plan to run many research jobs.</td><td style="word-wrap: break-word;">gpt-4o-mini</td></tr>
<tr><td style="word-wrap: break-word;">smart_llm_model</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Primary LLM model for core reasoning and report writing. Also taken from `get_llm_models()` and passed as `openai:<model_name>`. A stronger model here usually produces more accurate, better-organized, and more nuanced reports.</td><td style="word-wrap: break-word;">gpt-4o-2024-08-06</td></tr>
<tr><td style="word-wrap: break-word;">strategic_llm_model</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">LLM model used for high-level planning and strategic reasoning steps inside the research workflow (for example, designing research plans or strategies). Selected from `get_llm_models()` and sent as `openai:<model_name>`. Often configured to an advanced reasoning model.</td><td style="word-wrap: break-word;">o1-preview</td></tr>
<tr><td style="word-wrap: break-word;">source_urls</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of explicit URLs to use as research sources. When non-empty and `combine_source_urls_with_web` is false, the research is limited to these static pages (no automatic deep crawling). When empty, the agent relies on general web search and/or local documents depending on other settings.</td><td style="word-wrap: break-word;">https://openai.com/pricing, https://cloud.google.com/vertex-ai, https://azure.microsoft.com/en-us/products/ai-services</td></tr>
<tr><td style="word-wrap: break-word;">combine_source_urls_with_web</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether general web search should supplement the given `source_urls`. If true, the backend uses both your URLs and web search; if false and `source_urls` is non-empty, it treats the sources as static and restricts research to those pages only.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">local_documents_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem path (on the backend) to a directory containing local documents that should be included in the research. When non-empty, the node includes this as `DOC_PATH` in the config and sets the research mode to consider these documents alongside other sources.</td><td style="word-wrap: break-word;">/mnt/workspace/clients/acme_corp/research_docs/q1_2025/</td></tr>
<tr><td style="word-wrap: break-word;">combine_local_documents_with_web</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Flag that affects how web search is combined with static URLs when local documents are involved. If `source_urls` is set and this is false, the node initially uses a static mode for URL sources; when a `local_documents_path` is also provided, the effective mode becomes hybrid so documents are considered too. Adjust this if you want to strictly limit or broaden beyond the explicit URLs.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">report</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The final research report text returned by the backend. Its tone, structure, and detail follow the selected `report_type` and `report_format`, and it is typically a multi-section narrative suitable for direct presentation or further processing.</td><td style="word-wrap: break-word;"># Market Overview: Vector Databases for AI Retrieval (2025)  ## 1. Introduction This report analyzes leading vector database providers for AI retrieval applications in 2025...  ## 2. Key Providers - Provider A – fully managed cloud service with multi-region support and SOC 2 compliance... - Provider B – open-source, self-hosted, strong Kubernetes integration...  ## 3. Pricing Comparison - Provider A: usage-based pricing per million vector operations...  ## 4. Recommendations For a mid-sized EU fintech with strict data residency needs, the most suitable options are...</td></tr>
<tr><td style="word-wrap: break-word;">references</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Stringified representation (typically JSON) of the sources cited in the report, such as URLs, titles, and metadata. Downstream nodes can parse this string to generate citation lists, link previews, or validation/fact-checking workflows.</td><td style="word-wrap: break-word;">[{"url":"https://openai.com/pricing","title":"OpenAI Pricing","source_type":"web","accessed_at":"2025-03-10T14:35:12Z"},{"url":"https://cloud.google.com/vertex-ai","title":"Vertex AI Documentation","source_type":"web","accessed_at":"2025-03-10T14:40:05Z"}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a network call to an external research endpoint with a default timeout of 180 seconds; broad or complex queries can be slow, so avoid multiple sequential Web Research Agent calls in latency-sensitive workflows.
- **Limitations**: `source_urls` are treated as individual static pages with no automatic deep site traversal; you must list each URL you care about, and external web pages may change over time, affecting reproducibility.
- **Behavior**: When `task_context` is provided, it is appended to the main query via an internal prompt template; very large or unfocused context blocks can shift the question or increase token usage, so keep them relevant and concise.
- **Behavior**: The node sets an internal `report_source` mode (`web`, `static`, or `hybrid`) based on `source_urls`, `combine_source_urls_with_web`, and `local_documents_path`, which governs how strongly the backend relies on web search versus fixed URLs and local documents.
- **Limitations**: The node depends on a correctly configured `gpt_researcher_query` endpoint; misconfiguration, lack of connectivity, or service downtime will cause it to raise an exception without returning partial results.
- **Performance**: Model choices for `fast_llm_model`, `smart_llm_model`, and `strategic_llm_model` impact both cost and latency; use lighter models for exploratory or bulk research and reserve premium models for high-stakes, detailed analyses.

## Troubleshooting
- **Request to GPT Researcher service failed**: If an error message indicates the request to the GPT Researcher service failed with a status code and details, the backend endpoint is unreachable or returned an error. Check service configuration, network connectivity, and any rate limits or quotas, then retry.
- **Error decoding JSON response**: An error mentioning JSON decoding means the backend returned malformed or non-JSON content. This is typically a backend issue; retry later and, if it persists, contact platform support with the raw response for investigation.
- **Report is generic or off-topic**: If the report does not match expectations, refine `query` with clearer scope and constraints, add specific `task_context` (such as target audience and key decisions), and ensure `report_type` and `report_format` align with the intended style. Using a stronger `smart_llm_model` can also improve quality.
- **References missing or not useful**: When `references` omit expected sites, review `source_urls`, `combine_source_urls_with_web`, and `local_documents_path`. To focus on particular domains, list them in `source_urls` and set `combine_source_urls_with_web` to false so general web search does not dilute them.
- **Local documents appear unused**: If internal files do not seem to influence the report, verify that `local_documents_path` points to a directory accessible to the backend, that it contains supported document types, and that there are no extra spaces or typos in the path. After corrections, re-run the workflow.
