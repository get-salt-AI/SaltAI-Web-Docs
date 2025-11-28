# Groq LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a Large Language Model hosted by Groq through Salt’s LiteLLM integration. Supports dynamic prompt templating and standard LLM controls like temperature and max tokens. Automatically fetches available Groq models (with caching) and falls back to a built-in list if the service model list is unavailable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmgroq.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to generate text with Groq-hosted models in any workflow that needs summarization, analysis, drafting, or reasoning. Provide a system prompt for behavior/tone, a user prompt (with optional {{input_1}}–{{input_4}} placeholders for dynamic context), select a model, and tune temperature and max_tokens as needed. Chain it after context-gathering nodes and before downstream processing or output nodes.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Groq model to use. The list is fetched from the service and cached; falls back to a predefined set if fetching fails.</td><td style="word-wrap: break-word;">llama-3.1-8b-instant</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level instructions controlling style, tone, and constraints. Leave empty to omit a system message.</td><td style="word-wrap: break-word;">Respond as a concise technical assistant. Use bullet points where helpful.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">User prompt. Supports placeholders {{input_1}}–{{input_4}} that are replaced with the provided optional inputs.</td><td style="word-wrap: break-word;">Summarize the following notes: {{input_1}}. Focus on action items.</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Creativity vs. determinism. Lower is more focused; higher is more diverse.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum tokens to generate for the response.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic context inserted via {{input_1}} in the prompt.</td><td style="word-wrap: break-word;">Quarterly sales notes: ...</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic context inserted via {{input_2}} in the prompt.</td><td style="word-wrap: break-word;">Competitor highlights: ...</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic context inserted via {{input_3}} in the prompt.</td><td style="word-wrap: break-word;">Customer feedback snippets: ...</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic context inserted via {{input_4}} in the prompt.</td><td style="word-wrap: break-word;">Product roadmap bullets: ...</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated text response from the selected Groq model.</td><td style="word-wrap: break-word;">Here are the key action items for next quarter...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model availability is fetched and cached per provider; if fetching fails, a fallback list of Groq models is used.
- Valid model selection is required; using a model name not in the available list will raise an error.
- Prompt supports {{input_1}}–{{input_4}} placeholders that get replaced with the optional input values exactly as provided.
- Default timeout is designed for longer LLM responses; very large prompts or slow models may approach this limit.
- System prompt is included only if non-empty; clear it to omit system-level instructions.

## Troubleshooting
- Model not found: Ensure you select a model from the provided dropdown. If the dynamic list failed to load, use one of the fallback models.
- Timeouts or long waits: Reduce max_tokens or simplify the prompt; try a faster model; verify network stability.
- Empty or generic output: Lower temperature for more focused results, add clearer instructions, or provide concrete context via optional inputs.
- Placeholders not replaced: Confirm the prompt contains {{input_1}}–{{input_4}} and the corresponding inputs are populated.
- Rate limits or service errors: Retry after a short delay or switch to another available model. If persistent, reduce request frequency or output length.
