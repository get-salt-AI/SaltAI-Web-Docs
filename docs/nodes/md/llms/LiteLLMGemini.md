# Gemini LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries Google Gemini models through the LiteLLM service. It builds a chat-style request using a system prompt and a user prompt (with optional placeholders), then returns the model’s text response. Automatically lists available Gemini models from the service and falls back to a curated set if listing is unavailable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/LiteLLMGemini.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a Gemini LLM to generate or transform text based on prompts and optional contextual inputs. Typical workflows include summarization, content generation, classification, or instruction following where you can compose the final prompt using {{input_1}}–{{input_4}} placeholders and select the appropriate Gemini model level for speed vs. quality.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Choose a Gemini model to query. The list is fetched from the service; if unavailable, a fallback list is used.</td><td style="word-wrap: break-word;">gemini-2.5-pro</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level instructions and tone guidelines for the AI. If not empty, it is prepended as a system message.</td><td style="word-wrap: break-word;">You are a concise, helpful assistant. Respond with clear bullet points.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">Main user prompt. Supports placeholders {{input_1}}–{{input_4}} for injecting connected inputs at runtime.</td><td style="word-wrap: break-word;">Summarize the following notes: {{input_1}}. Highlight 3 key insights.</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness and creativity. Lower values are more deterministic.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens in the response. Use lower values for shorter, faster outputs.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context injected into {{input_1}} in the prompt.</td><td style="word-wrap: break-word;">Meeting notes from 2025-09-14</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context injected into {{input_2}} in the prompt.</td><td style="word-wrap: break-word;">Customer support transcript</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context injected into {{input_3}} in the prompt.</td><td style="word-wrap: break-word;">Product specification V3.2</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context injected into {{input_4}} in the prompt.</td><td style="word-wrap: break-word;">Known issues and FAQs</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text response returned by the selected Gemini model.</td><td style="word-wrap: break-word;">Here are three key insights from the notes: 1) ... 2) ... 3) ...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model selection is provider-scoped to Gemini and may fall back to a predefined list if live model listing is unavailable.
- Use {{input_1}}–{{input_4}} in the prompt to inject optional inputs dynamically.
- Temperature ranges from 0 to 1; start low for reliable results and increase for more creative output.
- Max tokens controls response length; higher values may increase latency and cost.
- Long-running requests are supported; however, very large prompts or responses may still hit service timeouts if set too low.

## Troubleshooting
- Model not found: Ensure you pick a model from the dropdown. If custom text was entered, select a listed option instead.
- Empty output or truncated response: Increase max_tokens or simplify your prompt/context.
- Inconsistent formatting: Lower the temperature and provide clearer instructions in the system_prompt.
- Slow or timed-out requests: Reduce prompt size, choose a faster model (e.g., a Flash variant), or increase the timeout setting if available in your environment.
- Unexpected literal placeholders: Make sure your prompt uses {{input_1}}–{{input_4}} exactly, and provide those optional inputs if needed.
