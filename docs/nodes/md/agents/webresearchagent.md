# Web Research Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs an automated web (and/or local documents) research workflow and returns a formatted report plus references. It crafts a research query from your input and task context, optionally restricts sources to given URLs or local files, and uses selected LLM/embedding models to generate results in a chosen format.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/webresearchagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need structured, citation-backed research on a topic. Provide a clear query, select the desired report type and output format, and optionally constrain sources (URLs or local documents). Integrate it into workflows that require research summaries, comparative analyses, or data-oriented outputs (CSV/JSON) for downstream processing.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The main research question or topic to investigate. Can be multi-line.</td><td style="word-wrap: break-word;">Assess the market outlook for residential solar in the EU over the next 5 years.</td></tr>
<tr><td style="word-wrap: break-word;">report_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Specifies the style of output. Options: Direct Answer, Concise Summary, Detailed Report, Comparative Analysis, News Article, How-To Guide, Creative Writing.</td><td style="word-wrap: break-word;">Detailed Report</td></tr>
<tr><td style="word-wrap: break-word;">report_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Specifies the output format. Options: Plain Text, Markdown, CSV, JSON, HTML, LaTeX.</td><td style="word-wrap: break-word;">Markdown</td></tr>
<tr><td style="word-wrap: break-word;">task_context</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional context to guide the research (e.g., form inputs, target schema, constraints). Leave empty if not needed.</td><td style="word-wrap: break-word;">Audience: non-technical executives. Focus on installation cost trends and policy incentives. Include a short risks section.</td></tr>
<tr><td style="word-wrap: break-word;">embed_model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Embedding model used to gather and rank relevant context.</td><td style="word-wrap: break-word;">text-embedding-3-small</td></tr>
<tr><td style="word-wrap: break-word;">fast_llm_model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">LLM for fast operations like brief summaries or lightweight steps.</td><td style="word-wrap: break-word;">gpt-4o-mini</td></tr>
<tr><td style="word-wrap: break-word;">smart_llm_model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">LLM for more complex reasoning and full report generation.</td><td style="word-wrap: break-word;">gpt-4o-2024-08-06</td></tr>
<tr><td style="word-wrap: break-word;">strategic_llm_model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">LLM for strategic planning tasks (research plans and strategies) within the workflow.</td><td style="word-wrap: break-word;">o1-preview</td></tr>
<tr><td style="word-wrap: break-word;">source_urls</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated list of URLs to restrict the research sources. No page traversal beyond provided URLs. Leave empty to use general web search.</td><td style="word-wrap: break-word;">https://www.iea.org/reports/solar-pv, https://ec.europa.eu/energy_topics</td></tr>
<tr><td style="word-wrap: break-word;">combine_source_urls_with_web</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, combines general web search with the provided source URLs. If false, research will be limited to the specified URLs when provided.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">local_documents_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filesystem path to a folder of local documents to include in the research. If set, the node will incorporate these documents into the evidence.</td><td style="word-wrap: break-word;">/data/research/solar_policy_docs</td></tr>
<tr><td style="word-wrap: break-word;">combine_local_documents_with_web</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes default web search in addition to local documents. If false and only local docs are provided, research emphasizes local materials.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">report</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated research report in the selected report format and style.</td><td style="word-wrap: break-word;">## EU Residential Solar Outlook (2025–2030) ... (Markdown content)</td></tr>
<tr><td style="word-wrap: break-word;">references</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference list or citations used to generate the report. Typically includes URLs and/or document identifiers.</td><td style="word-wrap: break-word;">- https://www.iea.org/...  - https://ec.europa.eu/...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Report formatting is strict**: The output is crafted to match the selected report_format (e.g., CSV/JSON/HTML). Downstream steps should expect that exact format.
- **Source control**: Providing source_urls limits research to those URLs unless you set combine_source_urls_with_web to true.
- **Local documents**: Setting local_documents_path includes local files; ensure the path is accessible to the backend environment executing the research.
- **Context injection**: task_context is appended to the research prompt; include schemas for CSV/JSON outputs to get structured data.
- **Model selection**: Different LLMs are used for fast, smart, and strategic steps; choose models appropriate for your cost/quality needs.
- **Timeouts**: Requests generally time out after several minutes; very broad or complex queries may require narrowing the scope.
- **Usage limits**: Access to this node may be subject to organizational limits for web research usage.

## Troubleshooting
- **Service error or timeout**: If you get an error or no response, try simplifying the query, reducing scope, or verifying connectivity. Consider using fewer or more specific sources.
- **Empty or low-quality references**: Provide source_urls to enforce credible sources, or add task_context specifying citation requirements.
- **Wrong output format**: If the report content doesn’t conform (e.g., invalid CSV/JSON), clarify the desired schema in task_context and retry.
- **Local documents not found**: Verify local_documents_path exists and is reachable by the backend. Use absolute paths and correct permissions.
- **Model not available**: If a selected model fails, switch to another supported model or defaults.
- **Results too generic**: Add detailed task_context (audience, required sections, metrics, comparison criteria) and select a more capable smart_llm_model.
