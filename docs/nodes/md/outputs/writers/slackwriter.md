# Slack Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Slack Writer sends messages to Slack using a Slack Bot token via the Slack WebClient. It supports plain text messages and rich Block Kit JSON payloads, can post to channels or user direct messages, and can reply in or start threads via a thread timestamp. The node outputs the final message text, the raw Slack API response as JSON, and the thread timestamp for reuse in subsequent Slack messages.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/outputs/writers/slackwriter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Slack Writer in Salt workflows when you need to push notifications, summaries, or interactive content into Slack. Common scenarios include reporting completion of data or ML jobs, sending monitoring alerts, posting AI-generated summaries, or maintaining threaded status updates. Place this node near the end of your pipeline after content-generation or analysis nodes, passing their outputs into `message` or into a JSON structure for `block_kit_json`. Set `channel` to a channel name/ID to post into team channels, or to a user ID to automatically open a direct message. To build threaded conversations, capture the `thread_timestamp` output from the first message and feed it into the `thread_timestamp` input of later Slack Writer nodes so all updates stay in the same Slack thread. For rich UI messages, generate Block Kit blocks upstream, test them with Slack’s Block Kit Builder, then pass the resulting JSON into `block_kit_json`. Ensure the bot token has required scopes like `chat:write` and `chat:write.customize` (for icons), and keep it stored securely.

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
<tr><td style="word-wrap: break-word;">slack_token</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Slack Bot authentication token used to authorize API calls. Must be a valid bot token with at least the `chat:write` permission and `chat:write.customize` if you want to customize icons via `icon_emoji` or `icon_url`. This field cannot be empty; if no token is provided the node raises an error and does not send anything.</td><td style="word-wrap: break-word;"><slack-bot-token></td></tr>
<tr><td style="word-wrap: break-word;">channel</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Slack destination where the message will be posted. Accepts a channel name (for example, `#general`), a channel ID (for example, `C05NNPR6A42`), or a user ID (for example, `U031AFC238B`). If the value starts with `U`, the node assumes it is a user ID and first opens a direct message channel using Slack’s conversations API, then posts to that DM channel. The bot must have access to the specified channel or user.</td><td style="word-wrap: break-word;">#alerts-ops</td></tr>
<tr><td style="word-wrap: break-word;">icon_emoji</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional emoji code (such as `:robot_face:`) used as the bot icon for this message, in place of the default bot icon or any `icon_url`. This requires the `chat:write.customize` scope on your Slack Bot. Leave blank to use Slack’s default icon configuration.</td><td style="word-wrap: break-word;">:satellite:</td></tr>
<tr><td style="word-wrap: break-word;">icon_url</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional URL to an image (PNG or JPEG, typically 512x512 pixels or smaller) to use as the bot’s icon for this message. Also requires the `chat:write.customize` scope. If both `icon_emoji` and `icon_url` are supplied, Slack’s own rules determine which one is applied; for clarity, prefer using just one.</td><td style="word-wrap: break-word;">https://example.com/images/salt-bot-256.png</td></tr>
<tr><td style="word-wrap: break-word;">thread_timestamp</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional Slack message timestamp (ts), such as `1734479286.246919`. When provided, the new message will be posted as a reply in the thread associated with that timestamp instead of creating a new top-level message. Use this to keep multi-step updates or conversations grouped in a single Slack thread.</td><td style="word-wrap: break-word;">1734479286.246919</td></tr>
<tr><td style="word-wrap: break-word;">message</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Plain text body of the message. Supports multiline content. This value is used when `block_kit_json` is empty. If `block_kit_json` is provided and valid, it overrides this field for the Slack message body; the node then uses a generated fallback text from the blocks instead of the `message` input.</td><td style="word-wrap: break-word;">Daily training job finished successfully. Accuracy: 93.4%, F1: 0.89.</td></tr>
<tr><td style="word-wrap: break-word;">block_kit_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional JSON string describing a list of Slack Block Kit blocks. The JSON must be syntactically valid and the top-level structure must be a list. When provided, these blocks are sent as the message content, and the node automatically walks through the structure to extract text strings into a fallback `text` field for older or limited Slack clients. If parsing fails or the top-level type is not a list, the node raises a validation error and nothing is sent.</td><td style="word-wrap: break-word;">[{"type":"section","text":{"type":"mrkdwn","text":"*Model training complete*"}},{"type":"context","elements":[{"type":"mrkdwn","text":"Accuracy: 93.4% \| F1: 0.89"}]}]</td></tr>
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
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text of the message as stored by Slack. For plain text sends, this usually matches the `message` input. For Block Kit sends, this contains the fallback text automatically generated from the `block_kit_json` blocks. Use this for logs, audits, or downstream display.</td><td style="word-wrap: break-word;">Daily training job finished successfully. Accuracy: 93.4%, F1: 0.89.</td></tr>
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded string of the full Slack API response object. This typically includes fields like `ok`, `channel`, `ts`, and a nested `message` payload. Downstream nodes can parse this string into structured data to inspect metadata, debug errors, or extract additional IDs.</td><td style="word-wrap: break-word;">{"ok": true, "channel": "C05NNPR6A42", "ts": "1734479286.246919", "message": {"type": "message", "text": "Daily training job finished successfully. Accuracy: 93.4%, F1: 0.89.", "user": "U03ABCDEF"}}</td></tr>
<tr><td style="word-wrap: break-word;">thread_timestamp</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Slack thread timestamp (ts) associated with the message. For a new top-level post, this is the message’s own ts. For a reply to an existing thread, this is still the top-level thread ts. Feed this value into future Slack Writer nodes’ `thread_timestamp` input to continue posting in the same thread.</td><td style="word-wrap: break-word;">1734479286.246919</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution makes at least one HTTP request to Slack and may make an additional `conversations.open` call when a user ID is provided as `channel`. High-frequency use can hit Slack rate limits; consider batching or reducing message volume.
- **Limitations**: The `block_kit_json` input is only validated for JSON syntax and that the top-level object is a list. It does not deeply validate each block against Slack’s full Block Kit schema, so some structurally invalid blocks may still trigger Slack-side errors.
- **Behavior**: When valid `block_kit_json` is supplied, it overrides `message` as the primary content. The node traverses the blocks to gather all string values stored under `text` keys and concatenates them into a newline-separated fallback plain-text message.
- **Behavior**: If the `channel` value starts with `U`, Slack Writer treats it as a user ID, calls Slack’s conversations API to open a direct message channel, and then posts to that channel ID. If this resolution fails, the node raises a specific error about failing to resolve the direct message channel.
- **Security**: The Slack token is mandatory. On error, the implementation attempts to redact the actual token value from error strings before logging or raising them, but you should still handle logs and error messages as sensitive information.
- **Scopes**: Using `icon_emoji` or `icon_url` for custom icons requires the `chat:write.customize` scope for your Slack Bot. Without it, custom icons may be ignored or may cause permission-related errors from Slack.

## Troubleshooting
- **Missing token error**: If you see an error stating that a Slack bot token is required, check that the `slack_token` input is provided and that it is a valid bot token with `chat:write` enabled in your Slack app configuration.
- **Invalid Block Kit JSON**: If you receive an error message about invalid Block-Kit JSON or a JSON decoding failure, verify that `block_kit_json` is well-formed JSON and that the top-level structure is a list. Use Slack’s Block Kit Builder to construct and validate the payload, then copy its JSON into the node.
- **Channel or permission issues**: Errors such as `Slack API error: channel_not_found` or `Slack API error: not_in_channel` indicate problems with the `channel` value or bot membership. Confirm that the channel name or ID is correct and that the bot has been added to that channel.
- **Direct message resolution failures**: If the node reports a failure to resolve a direct message channel ID, ensure that the `channel` input is a valid Slack user ID (for example, `U031AFC238B`) and that the bot is allowed to open direct messages with that user in your workspace.
- **Timeouts or slow responses**: If the node appears to hang or returns a timeout error, it may be due to network latency or Slack-side delays. Consider increasing the hidden `timeout` input within its allowed range and verify your network connectivity and Slack’s service status.
