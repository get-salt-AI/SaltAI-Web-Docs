# Tavily Web Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the Tavily web search service and returns both a concise natural-language answer (if enabled) and structured search documents. It supports configurable search depth, topic scoping between general and news, domain inclusion and exclusion, and control over whether raw page content is included. It is designed for retrieval-augmented generation, research agents, and workflows that need fresh information from the web.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/websearchtavily.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when your workflow needs fresh or external web information beyond your local data. Common scenarios include question answering over current events, building research assistants that synthesize information from multiple sources, and augmenting long-form content generation with recent citations. In a typical pipeline, an upstream node such as a user input node, prompt builder, or agent planner crafts the query string based on user intent. WebSearchTavily then performs the web search using the configured depth, topic, and domain filters. Downstream, the answer output can be sent directly to a response node or used as a reasoning hint, while the documents output is parsed and fed into summarization, ranking, or retrieval-augmented generation nodes. Best practices include starting with basic search_depth for lower cost and latency, switching to advanced for complex research; using the news topic with the days parameter for time-sensitive queries; constraining or filtering sources with include_domains and exclude_domains; and enabling include_raw_content only when you truly need full page text, to avoid large payloads.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The natural-language search query to send to Tavily. This can be a question, topic, or concise description of the information you need. Extremely long or unfocused queries may degrade search quality, so keep it specific and goal-oriented.</td><td style="word-wrap: break-word;">Summarize the latest open-source large language models released in the last 7 days and compare their benchmark performance.</td></tr>
<tr><td style="word-wrap: break-word;">search_depth</td><td>True</td><td style="word-wrap: break-word;">["basic", "advanced"]</td><td style="word-wrap: break-word;">Controls how deep Tavily searches and processes content. Use basic for faster, lower-cost searches that are usually sufficient for straightforward questions. Use advanced for more thorough, higher-recall searches when investigating complex or multi-faceted topics.</td><td style="word-wrap: break-word;">basic</td></tr>
<tr><td style="word-wrap: break-word;">max_results</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The maximum number of search results to return. Must be at least 1. Higher values provide more source coverage but also increase response size, latency, and downstream processing cost.</td><td style="word-wrap: break-word;">6</td></tr>
<tr><td style="word-wrap: break-word;">topic</td><td>True</td><td style="word-wrap: break-word;">["general", "news"]</td><td style="word-wrap: break-word;">Specifies the category of the search. Choose general for broad web information needs without strict recency constraints. Choose news for time-sensitive, news-focused queries; this activates the days parameter to limit the recency window.</td><td style="word-wrap: break-word;">news</td></tr>
<tr><td style="word-wrap: break-word;">days</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Only meaningful when topic is set to news. Defines how many days back from the current date the search should consider, effectively constraining results to a recent time window. For example, 3 restricts results to approximately the last three days.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">include_answer</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, Tavily generates and returns a concise natural-language answer summarizing the search results for the query. Set this to false when you only want the raw documents to power your own reasoning or custom aggregation logic.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">include_raw_content</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, each document may include raw page text content. When false, only the most query-relevant extracted segments are returned. Raw content offers maximum context but significantly increases payload size and downstream token usage, so use it only when required.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">include_domains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional newline-separated list of domains that Tavily should include or prioritize in the search results. Use this to restrict or bias searches toward trusted, topic-specific, or organization-approved sources. Provide one bare domain per line, without protocol or path. Leave empty to search broadly.</td><td style="word-wrap: break-word;">arxiv.org huggingface.co nature.com</td></tr>
<tr><td style="word-wrap: break-word;">exclude_domains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional newline-separated list of domains that Tavily should exclude from results. Use this to filter out low-quality, paywalled, or otherwise undesired sources. Provide one bare domain per line without http or https prefixes.</td><td style="word-wrap: break-word;">reddit.com quora.com pinterest.com</td></tr>
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
<tr><td style="word-wrap: break-word;">answer</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A concise natural-language answer summarizing the search results when include_answer is true. Suitable for direct display to users or as a high-level reasoning hint for downstream language model nodes. If include_answer is false or no summary is generated, this string may be empty or minimal.</td><td style="word-wrap: break-word;">Several new open-source large language models were released in the past week, including updated LLaMA-derived models and compact architectures optimized for edge devices. Early benchmarks indicate that many of these match or approach the performance of proprietary systems on common instruction-following and reasoning benchmarks.</td></tr>
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A STRING containing a structured representation, typically JSON, of the retrieved search documents. Each entry usually includes fields such as title, url, and content, where content is either AI-selected relevant text or full raw page text depending on include_raw_content. Downstream components are expected to parse and process this string for retrieval-augmented generation, summarization, or citation extraction.</td><td style="word-wrap: break-word;">[{"title": "Open-source LLMs in 2026", "url": "https://arxiv.org/abs/2601.01234", "content": "This survey reviews the latest open-source large language models..."}, {"title": "Efficient LLMs for Edge Devices", "url": "https://huggingface.co/blog/efficient-llms", "content": "We present a family of lightweight LLMs optimized for on-device inference..."}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Advanced search_depth performs more exhaustive retrieval and processing at the cost of higher latency and resource usage; reserve it for complex or high-importance queries.
- **Performance**: Enabling include_raw_content can substantially increase the size of the documents output and may push downstream models toward token or memory limits; keep it disabled unless full text is necessary.
- **Limitations**: When using the news topic, very small days windows or extremely niche topics may result in few or no results, even for otherwise well-formed queries.
- **Behavior**: The days parameter is practically applied only when topic is set to news; in other modes it does not constrain search recency even though it remains part of the node configuration.
- **Behavior**: The include_domains and exclude_domains inputs expect bare domains without protocol or path segments; malformed entries can be ignored or lead to unintuitive filtering.
- **Behavior**: The documents output is provided as a STRING containing structured data, typically JSON; most downstream nodes must parse it before performing detailed inspection or token-level operations.

## Troubleshooting
- **No or very few results**: If documents is empty or almost empty, increase max_results, switch search_depth from basic to advanced, relax or clear include_domains, and consider broadening or rephrasing the query.
- **Outdated results for time-sensitive questions**: Confirm that topic is set to news and that days is set to a reasonable positive value, such as between 1 and 7. If topic is general, the days setting will not limit recency.
- **Downstream model hitting token limits or slow responses**: Reduce max_results, turn off include_raw_content, or insert a summarization or filtering step that condenses documents before sending them to a language model.
- **Unwanted or low-quality sources appearing**: Add problematic sites to exclude_domains and optionally specify a curated list in include_domains to focus on trusted sources, then rerun the same query.
- **Errors when consuming documents output**: Remember that documents is a STRING with structured content. Ensure downstream nodes expect a serialized payload and, if needed, parse the string into a structured format before further processing.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/websearchtavily/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

