# Web Research Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs an end-to-end web research workflow for a given query and returns a structured report plus references. You can constrain research to specific URLs and/or include local documents, and select different LLM/embedding models for speed, quality, and strategy. The node composes a report request from your inputs and calls a backend research service to generate results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/webresearchagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a concise or in-depth research report with cited sources. Typical flow: provide your research query, choose a report type and format, optionally add task context, and decide whether to target specific URLs, include local documents, or allow broader web search. Adjust model choices for fast summaries versus deep reasoning. The output includes the final report and a references list.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Your research question or topic. This is the main prompt that the agent investigates.</td><td style="word-wrap: break-word;">What are the latest trends and risks in using RAG for enterprise search?</td></tr>
<tr><td style="word-wrap: break-word;">report_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The style/purpose of the report. Options are the available keys from report_types (e.g., overview, deep_dive, competitive_analysis; exact options depend on environment).</td><td style="word-wrap: break-word;">deep_dive</td></tr>
<tr><td style="word-wrap: break-word;">report_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The output format for the report. Options are the available keys from report_formats (e.g., markdown, bullet_summary; exact options depend on environment).</td><td style="word-wrap: break-word;">markdown</td></tr>
<tr><td style="word-wrap: break-word;">task_context</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional context to tailor the research (e.g., form inputs, audience, constraints). Appended to the query to guide the agent.</td><td style="word-wrap: break-word;">Audience: CTOs in fintech; Emphasize compliance and cost trade-offs.</td></tr>
<tr><td style="word-wrap: break-word;">embed_model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Embedding model used to retrieve relevant context. Provided as a choice from available embedding models.</td><td style="word-wrap: break-word;">text-embedding-3-small</td></tr>
<tr><td style="word-wrap: break-word;">fast_llm_model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">LLM for fast operations like quick summaries.</td><td style="word-wrap: break-word;">gpt-4o-mini</td></tr>
<tr><td style="word-wrap: break-word;">smart_llm_model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">LLM for higher-quality reasoning and composing the main report.</td><td style="word-wrap: break-word;">gpt-4o-2024-08-06</td></tr>
<tr><td style="word-wrap: break-word;">strategic_llm_model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">LLM for strategy-heavy tasks (planning the approach, complex reasoning).</td><td style="word-wrap: break-word;">o1-preview</td></tr>
<tr><td style="word-wrap: break-word;">source_urls</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Limit research to specific sources (no page traversal). Provide a single URL or a comma-separated list.</td><td style="word-wrap: break-word;">https://openai.com/research, https://arxiv.org</td></tr>
<tr><td style="word-wrap: break-word;">combine_source_urls_with_web</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, also perform a general web search in addition to the provided source_urls.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">local_documents_path</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem path to a folder of local documents to include in the research corpus.</td><td style="word-wrap: break-word;">/workspace/docs/rag-evaluations</td></tr>
<tr><td style="word-wrap: break-word;">combine_local_documents_with_web</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, also perform web search in addition to local documents.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">report</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated research report in the selected format.</td><td style="word-wrap: break-word;"># RAG in Enterprise Search: 2025 Outlook  1. Summary... </td></tr>
<tr><td style="word-wrap: break-word;">references</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A serialized list or text block of sources cited in the report.</td><td style="word-wrap: break-word;">["https://arxiv.org/abs/2401.12345", "https://openai.com/blog/..." ]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Report scope**: If only source_urls are provided and web search is not combined, research is constrained to those URLs; adding local_documents_path enables a hybrid mode that includes local files.
- **Input formatting**: source_urls must be comma-separated; no automatic site crawling or pagination is performed for a base URL.
- **Context handling**: task_context is appended to the query to guide the agent’s focus and tone.
- **Model selection**: The node prefixes models internally for the backend (e.g., openai:model-name). Ensure chosen models are available in your environment.
- **Service dependency**: This node calls an external research service and may take up to ~180 seconds to complete; failures from the service propagate as errors.
- **Access limits**: This node may be subject to workspace or plan limits; exceeding limits can block execution.

## Troubleshooting
- **Non-200 error from research service**: Verify your workspace has access and that the backend endpoint is configured. Try again later or simplify the query.
- **Timeouts or long waits**: Reduce scope (fewer source_urls, shorter query), choose faster models, or try again during lower load.
- **Empty or low-quality results**: Provide clearer task_context, pick a more capable smart_llm_model, add targeted source_urls or local documents.
- **Invalid source_urls parsing**: Ensure URLs are comma-separated with no trailing commas and include a scheme (https://).
- **Local documents not included**: Confirm local_documents_path exists and is accessible; set combine_local_documents_with_web if you also expect web sources.
- **Unexpected format**: Check the selected report_format and report_type; choose a different option better aligned with your needs.
