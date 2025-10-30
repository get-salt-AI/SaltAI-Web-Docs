# Web Research Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs guided web research on a user query and returns a structured report with references. You can constrain sources to specific URLs and/or include local documents, while selecting different LLM roles (fast, smart, strategic) and an embedding model to influence research quality and speed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/Agents/WebResearchAgent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need an AI-generated research summary or report grounded in web sources and optional local files. Provide a clear query, pick a report type and format, optionally add task context, restrict or augment sources via URLs and local documents, then connect the outputs to downstream nodes that consume the report and its reference list.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The research question or topic to investigate. Supports multiline input.</td><td style="word-wrap: break-word;">What are the latest techniques for RAG evaluation in production systems?</td></tr>
<tr><td style="word-wrap: break-word;">report_type</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Select the research style or template (e.g., deep-dive, overview, competitor analysis). Determines how the agent plans and composes the report.</td><td style="word-wrap: break-word;">deep_dive</td></tr>
<tr><td style="word-wrap: break-word;">report_format</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Select the output format (e.g., markdown, bullet_points, executive_summary). Controls the structure and presentation of the final report.</td><td style="word-wrap: break-word;">markdown</td></tr>
<tr><td style="word-wrap: break-word;">task_context</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional additional context (such as form data or constraints) to steer the research. Appended to the query internally.</td><td style="word-wrap: break-word;">Audience: CTO; Focus on actionable recommendations and recent benchmarks.</td></tr>
<tr><td style="word-wrap: break-word;">embed_model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Embedding model used for retrieving and ranking relevant context.</td><td style="word-wrap: break-word;">text-embedding-3-small</td></tr>
<tr><td style="word-wrap: break-word;">fast_llm_model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">LLM used for fast operations such as quick summaries.</td><td style="word-wrap: break-word;">gpt-4o-mini</td></tr>
<tr><td style="word-wrap: break-word;">smart_llm_model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">LLM used for higher-quality reasoning and report generation.</td><td style="word-wrap: break-word;">gpt-4o-2024-08-06</td></tr>
<tr><td style="word-wrap: break-word;">strategic_llm_model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">LLM used for strategic planning steps (e.g., research plans and strategies).</td><td style="word-wrap: break-word;">o1-preview</td></tr>
<tr><td style="word-wrap: break-word;">source_urls</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of specific URLs to search within. When provided, the research will prioritize or limit to these sources.</td><td style="word-wrap: break-word;">https://arxiv.org, https://openai.com/research</td></tr>
<tr><td style="word-wrap: break-word;">combine_source_urls_with_web</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, also performs general web search in addition to the provided source URLs.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">local_documents_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to a folder of local documents to include in the research corpus.</td><td style="word-wrap: break-word;">/workspace/research_docs/</td></tr>
<tr><td style="word-wrap: break-word;">combine_local_documents_with_web</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true and local documents are provided, also include general web sources; otherwise limit to provided sources.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">report</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated research report in the selected format.</td><td style="word-wrap: break-word;"># RAG Evaluation in Production  1. Overview... 2. Methods... </td></tr>
<tr><td style="word-wrap: break-word;">references</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A stringified collection of references/citations used in the report.</td><td style="word-wrap: break-word;">[{"title":"Paper A","url":"https://arxiv.org/abs/xxx"},{"title":"Blog B","url":"https://example.com"}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Usage limits may apply**: This node is categorized under Web Research and may be subject to workspace limits.
- **Source control**: Providing source_urls restricts the research to those sites unless you enable combining with general web search.
- **Local documents**: Supplying a local_documents_path includes your local files in the research; optionally combine with web sources.
- **Model selection matters**: Fast/Smart/Strategic LLM choices and the embedding model affect latency, quality, and cost.
- **Input formatting**: For multiple URLs, provide them as a comma-separated list without spaces or with spaces trimmed.

## Troubleshooting
- **Timeouts or failing requests**: Ensure network connectivity and try again; large or complex queries may require simplifying the prompt or adjusting model selections.
- **Service error received**: Verify your user permissions/limits and that the selected models are available; retry later if the research service is temporarily unavailable.
- **Invalid or empty references**: Confirm that the query and sources are specific enough; broaden sources or enable combining with web search.
- **No results from local documents**: Check that local_documents_path is correct and accessible, and that files are supported/readable.
- **Unexpected output format**: Re-run with a different report_format and confirm downstream nodes expect a STRING output.
