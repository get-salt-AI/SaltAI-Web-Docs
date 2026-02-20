# Anthropic LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sends a text prompt to Anthropic models (Claude family) through the LiteLLM provider and returns the model’s text response. Supports a system prompt, temperature control, and maximum token limits, with convenient optional inputs for templating or dynamic context. Includes built-in fallback model name mappings to help resolve common Anthropic model identifiers.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmanthropic.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need a Claude (Anthropic) model to generate, summarize, translate, or reason over text. Typically, you will set the model, provide a system prompt to define behavior, pass the main user prompt (optionally enriched with input_1..input_4), and tune temperature and max_tokens. Connect its string output to downstream logic, routing, or storage nodes.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Anthropic model identifier to use. Accepts Anthropic Claude variants. Common examples are mapped via internal fallbacks.</td><td style="word-wrap: break-word;">claude-3-5-sonnet-20241022</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level instructions guiding the model’s behavior and style for the entire session.</td><td style="word-wrap: break-word;">You are a concise, helpful assistant. Provide clear bullet-point answers.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The main user message or task description sent to the model. You may incorporate optional inputs as context.</td><td style="word-wrap: break-word;">Summarize the following text and extract key actions: {input_1}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness (0=deterministic, 1=creative). Higher values increase variability.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens to generate in the response. Must be within the selected model’s limits.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional auxiliary text input for templating or adding extra context to the prompt.</td><td style="word-wrap: break-word;">Customer message: I can’t log in to my account after resetting my password.</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional auxiliary text input for templating or adding extra context to the prompt.</td><td style="word-wrap: break-word;">Relevant policy: Users must confirm their email after password reset.</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional auxiliary text input for templating or adding extra context to the prompt.</td><td style="word-wrap: break-word;">Known issue: Email delivery delays reported in region X.</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional auxiliary text input for templating or adding extra context to the prompt.</td><td style="word-wrap: break-word;">Support guidelines: Always verify account ownership before changes.</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated text from the selected Anthropic model.</td><td style="word-wrap: break-word;">Here’s a concise summary with key actions and suggested next steps...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model selection: Use valid Anthropic model IDs (e.g., Claude family). The node includes fallback mappings to resolve common identifiers.
- Credentials: Ensure Anthropic access is configured in your environment or project settings before use. Never paste secrets directly into prompts; store them securely as environment or project variables.
- Token limits: max_tokens must not exceed the model’s limit. If set too high, the request may fail.
- Temperature tuning: Lower values (e.g., 0.0–0.3) are better for deterministic outputs; higher values (e.g., 0.7–1.0) encourage creativity.
- Optional inputs are pass-through strings you can reference in your prompt to add context or build templates.

## Troubleshooting
- Invalid or missing API credentials: Configure the Anthropic API key in your project or environment settings and retry.
- Model not found: Verify the model name matches an available Anthropic model or use a known fallback identifier.
- Output too long or truncated: Reduce response length by lowering max_tokens or by requesting a shorter answer in your prompt.
- Empty or low-quality responses: Provide a clearer system_prompt, add structured context via input_1..input_4, or lower temperature for more focused results.
- Rate limits or timeouts: Retry with exponential backoff, reduce request frequency, or simplify prompts to shorten processing time.
