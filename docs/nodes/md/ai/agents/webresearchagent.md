# Web Research Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node orchestrates an AI-powered research workflow over the web, optional fixed URLs, and optional local documents, then returns a structured report and supporting references. It builds a detailed research prompt from your query, report type, report format, and task context, then calls an external GPT Researcher service with configurable embedding and LLM models. The output is a human-readable report string and a references payload that downstream nodes can parse for citations, links, or further analysis.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/ai/agents/webresearchagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Web Research Agent when you need citation-backed, structured research on a topic instead of a simple Q&A response. Common use cases include market or competitor research, technical literature overviews, policy or regulatory summaries, comparative analyses, and data-oriented outputs like CSV/JSON that can be post-processed. Place this node after any step that formulates or refines the research question (for example, a form-processing node, a prior LLM planning node, or a user input collection node) and before nodes that consume the report (e.g., formatting, visualization, email generation, or database ingestion).

Configure the basic section first: enter a clear `query`, choose a `report_type` (e.g., overview, comparison, step-by-step guide—options are defined in the platform’s report_types list) and a `report_format` (narrative, bullet list, table-like, JSON, CSV, etc., as supported by report_formats). In the advanced section, optionally constrain or enrich sources by specifying `source_urls` (comma-separated URLs), toggling whether to combine them with general web search, and/or providing a `local_documents_path` to a folder of internal files you want included. Select suitable `embed_model`, `fast_llm_model`, `smart_llm_model`, and `strategic_llm_model` to balance cost vs. quality.

Typical upstream nodes:
- Input collection or form-parsing nodes that build the `query` and `task_context`.
- Workflow planners that decide what `report_type` and `report_format` to use.

Typical downstream nodes:
- Text processing nodes that reformat or shorten the `report` for emails, slide decks, or knowledge bases.
- Parsing/transformation nodes that interpret `references` for structured storage (e.g., extracting URLs, titles, and citation metadata).

Best practices:
- Provide a focused, specific query and use `task_context` for detailed constraints or background (e.g., target audience, domain, tone, or existing data).
- Use `source_urls` when you must restrict research to authoritative domains (e.g., company docs, regulator sites) and toggle `combine_source_urls_with_web` depending on whether external context is allowed.
- For internal or confidential analyses, favor `local_documents_path` so the agent can ground its output in your private corpus while optionally still augmenting with web resources.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Main research question or topic, ideally a clear, specific prompt describing what you want investigated. Supports multi-line text. This is the base search query used by the research backend.</td><td style="word-wrap: break-word;">What are the key privacy regulations affecting healthcare SaaS products operating in both the EU and US?</td></tr>
<tr><td style="word-wrap: break-word;">report_type</td><td>True</td><td style="word-wrap: break-word;">ENUM (keys of report_types)</td><td style="word-wrap: break-word;">High-level style or structure of the research deliverable. Options come from the platform-defined report_types map; each type has its own prompt template and tooltip. Use this to choose between e.g. overview, comparative analysis, implementation guide, risk assessment, etc.</td><td style="word-wrap: break-word;">comparative_analysis</td></tr>
<tr><td style="word-wrap: break-word;">report_format</td><td>True</td><td style="word-wrap: break-word;">ENUM (keys of report_formats)</td><td style="word-wrap: break-word;">Desired output format of the report. Backed by the report_formats map, which defines formatting instructions. Common patterns include narrative text, bullet points, markdown tables, or machine-readable formats like JSON or CSV for downstream processing.</td><td style="word-wrap: break-word;">markdown_table</td></tr>
<tr><td style="word-wrap: break-word;">task_context</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional context appended to the query via a context prompt template. Use this for constraints such as target audience, writing style, domain-specific assumptions, or extra data (e.g. form submissions) that should guide the research and report.</td><td style="word-wrap: break-word;">The audience is a non-technical executive team at a mid-size US healthcare SaaS company. Focus on strategic implications, not implementation details.</td></tr>
<tr><td style="word-wrap: break-word;">embed_model</td><td>True</td><td style="word-wrap: break-word;">ENUM (embedding models)</td><td style="word-wrap: break-word;">Embedding model identifier used by the research service for retrieving relevant context. The node prefixes this as `openai:<model>` in the config. Choose a model that balances speed and quality; smaller models are cheaper/faster, larger models can improve retrieval quality.</td><td style="word-wrap: break-word;">text-embedding-3-small</td></tr>
<tr><td style="word-wrap: break-word;">fast_llm_model</td><td>True</td><td style="word-wrap: break-word;">ENUM (LLM models)</td><td style="word-wrap: break-word;">LLM model for fast operations like quick summaries and intermediate expansions. Passed to the service as `openai:<model>`. Typically choose a lightweight, cost-efficient model suitable for high-volume or iterative calls in the research pipeline.</td><td style="word-wrap: break-word;">gpt-4o-mini</td></tr>
<tr><td style="word-wrap: break-word;">smart_llm_model</td><td>True</td><td style="word-wrap: break-word;">ENUM (LLM models)</td><td style="word-wrap: break-word;">Primary LLM for core reasoning and report writing. Used for generating the main research report and handling complex reasoning steps. Choose a higher-quality model when accuracy, nuance, and synthesis depth are critical.</td><td style="word-wrap: break-word;">gpt-4o-2024-08-06</td></tr>
<tr><td style="word-wrap: break-word;">strategic_llm_model</td><td>True</td><td style="word-wrap: break-word;">ENUM (LLM models)</td><td style="word-wrap: break-word;">LLM model for strategic planning aspects of the research—such as designing research plans, prioritizing sources, or outlining strategies. Use a more capable reasoning model if your use case relies heavily on planning and high-level decision support.</td><td style="word-wrap: break-word;">o1-preview</td></tr>
<tr><td style="word-wrap: break-word;">source_urls</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional list of specific sources to restrict or focus the research on. No link traversal is performed beyond these URLs. Accepts a single URL or multiple, comma-separated. If left blank, the research relies on general web search (plus local documents if provided).</td><td style="word-wrap: break-word;">https://www.hhs.gov/hipaa/, https://gdpr.eu/, https://www.edpb.europa.eu/</td></tr>
<tr><td style="word-wrap: break-word;">combine_source_urls_with_web</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, the research service will gather resources using the default web search in addition to the specified `source_urls`. If false and `source_urls` are provided, research is primarily constrained to those static sources (subject to how the backend interprets `report_source`).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">local_documents_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem path to a folder of local documents to include as part of the research corpus. When set, the node adds `DOC_PATH` to the config, and the service treats the source as hybrid (local + web, depending on other flags). Use this to ground research in internal reports, knowledge bases, or customer data extracts.</td><td style="word-wrap: break-word;">/mnt/team-docs/compliance/2025_q1_regulatory_updates</td></tr>
<tr><td style="word-wrap: break-word;">combine_local_documents_with_web</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether default web search results are combined with local documents and/or static URLs. If you provide `source_urls` but set this false, the node marks the source as static; when local documents are present, the final `report_source` is considered hybrid. Use this to keep research strictly internal or allow external augmentation.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">report</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The main research report text generated by the GPT Researcher service. Its structure and style follow the selected `report_type` and `report_format`, and it is grounded in the retrieved sources. Downstream nodes can render this as-is for users, further summarize it, convert it to slides, or store it in a knowledge base.</td><td style="word-wrap: break-word;">## Executive Summary This report compares HIPAA (US) and GDPR (EU) requirements for healthcare SaaS providers, focusing on data handling, consent, and breach notification...  ### 1. Scope and Applicability - HIPAA applies to covered entities and business associates handling PHI in the US... - GDPR applies to controllers and processors handling personal data of individuals in the EU...  ### 2. Key Obligations - Data minimization (GDPR Art. 5) vs. HIPAA's minimum necessary standard...  (continued...)</td></tr>
<tr><td style="word-wrap: break-word;">references</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A serialized references payload returned by the research service, typically a JSON-formatted string containing citations, URLs, titles, and possibly relevance scores or snippets. This is intended for downstream parsing to build reference lists, link cards in UIs, or structured storage of supporting evidence.</td><td style="word-wrap: break-word;">{   "references": [     {       "title": "Summary of the HIPAA Privacy Rule",       "url": "https://www.hhs.gov/hipaa/for-professionals/privacy/laws-regulations/index.html",       "source_type": "web",       "snippet": "The HIPAA Privacy Rule establishes national standards to protect individuals' medical records..."     },     {       "title": "General Data Protection Regulation (GDPR)",       "url": "https://gdpr.eu/what-is-gdpr/",       "source_type": "web",       "snippet": "The General Data Protection Regulation (GDPR) is the toughest privacy and security law in the world..."     }   ] }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs an HTTP POST to an external GPT Researcher service with a default timeout of 180 seconds; complex or broad queries can approach this limit, so avoid extremely open-ended prompts in latency-sensitive workflows.
- **Limitations**: `source_urls` do not trigger deep crawling or page traversal; only the specified pages are considered, so ensure you include all key URLs you care about.
- **Limitations**: The quality and structure of outputs depend heavily on the predefined `report_types` and `report_formats`; unsupported custom types or formats cannot be introduced directly through this node.
- **Behavior**: `report_source` is set programmatically (web, static, or hybrid) based on `source_urls`, `combine_*` flags, and `local_documents_path`, which affects how the backend fetches and weights sources; misconfigured combinations can unintentionally broaden or narrow the research scope.
- **Behavior**: The node assumes the external service returns JSON with `report` and `references` keys; if this contract changes upstream, workflows may fail until updated.
- **Performance**: Using large or premium LLM models for `smart_llm_model` and `strategic_llm_model` may increase cost and latency; reserve these for high-value research steps, and consider lighter models for prototyping.

## Troubleshooting
- **Request to GPT Researcher service failed**: If you see an exception mentioning this message (often with an HTTP status code and error detail), the external service may be unavailable or misconfigured. Check network connectivity, confirm the GPT Researcher endpoint is healthy in platform settings, and verify that your account has access.
- **Error decoding JSON response**: This indicates the service returned invalid or non-JSON content. It can happen if the backend throws an HTML error page or a plain-text error. Inspect service logs, validate that the endpoint URL in ENDPOINTS["gpt_researcher_query"] is correct, and retry with a simpler query to rule out payload-specific issues.
- **Empty or low-quality report**: If the report is vague or missing key details, refine the `query` and add richer `task_context` (target audience, domain constraints, examples). Also ensure `source_urls` and `local_documents_path` actually point to relevant and accessible content.
- **Missing or sparse references**: When `references` lack enough sources, confirm that `combine_source_urls_with_web` is configured as intended, and that the specified URLs are publicly reachable. If using only local documents, verify that `local_documents_path` is correct and that the service has permission to read those files.
- **Timeouts or long runtimes**: For consistently slow runs, narrow the scope of `query`, reduce the number of `source_urls`, or switch to faster models for `fast_llm_model` and possibly `smart_llm_model`. Consider splitting very large research tasks into multiple more focused WebResearchAgent calls in your workflow.
