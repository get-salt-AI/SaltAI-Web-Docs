# Slack Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sends messages to Slack channels, users, or threads using a Slack Bot token. Supports both plain text and rich Block Kit payloads, automatically generating a text fallback from Block Kit for broader client compatibility. Can reply in threads and customize bot icon via emoji or image URL.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/outputs/writers/slackwriter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to post notifications, status updates, or rich interactive messages to Slack during a workflow. Provide a valid Slack Bot token, target channel (name, channel ID, or user ID), and either plain text or a Block Kit JSON payload. For replies, pass a thread timestamp to post within an existing thread.

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
<tr><td style="word-wrap: break-word;">slack_token</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Slack Bot OAuth token used to authenticate API requests. Obtain via Slack's quick-start app setup.</td><td style="word-wrap: break-word;"><slack-bot-token></td></tr>
<tr><td style="word-wrap: break-word;">channel</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Destination in Slack. Accepts channel name (e.g., #general), channel ID (e.g., C0123456789), or user ID (e.g., U0123456789). User IDs are resolved to a DM channel automatically.</td><td style="word-wrap: break-word;">#alerts</td></tr>
<tr><td style="word-wrap: break-word;">icon_emoji</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Emoji to use as the bot icon (requires chat:write.customize). Ignored if empty or if icon_url is also set.</td><td style="word-wrap: break-word;">:robot_face:</td></tr>
<tr><td style="word-wrap: break-word;">icon_url</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Direct URL to an image to use as the bot icon (512x512 or smaller; requires chat:write.customize).</td><td style="word-wrap: break-word;">https://example.com/bot.png</td></tr>
<tr><td style="word-wrap: break-word;">thread_timestamp</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Slack message timestamp to reply to, posting the message in that thread.</td><td style="word-wrap: break-word;">1734479286.246919</td></tr>
<tr><td style="word-wrap: break-word;">message</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Plain text message content. Ignored if block_kit_json is provided.</td><td style="word-wrap: break-word;">Hello from Salt AI!</td></tr>
<tr><td style="word-wrap: break-word;">block_kit_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Block Kit JSON array for rich message content. Must be a JSON list of valid blocks. A text fallback is auto-generated from contained text items.</td><td style="word-wrap: break-word;">[{"type":"section","text":{"type":"mrkdwn","text":"Deployment complete."}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The posted message text returned by Slack. For Block Kit, this is the generated fallback text.</td><td style="word-wrap: break-word;">Hello from Salt AI!</td></tr>
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw Slack API response as a JSON string.</td><td style="word-wrap: break-word;">{"ok": true, "channel": "C0123456789", "ts": "1734479286.246919", ...}</td></tr>
<tr><td style="word-wrap: break-word;">thread_timestamp</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Timestamp of the created or replied-to message. Use this to continue the thread.</td><td style="word-wrap: break-word;">1734479286.246919</td></tr>
</tbody>
</table>
</div>

## Important Notes
- A valid Slack Bot token is required. Create and install a Slack app to obtain it.
- If channel is a user ID (starts with U), the node opens a DM channel automatically before posting.
- If block_kit_json is provided, it must be a JSON array of blocks; the message input is ignored.
- The node generates a plain-text fallback from Block Kit content to maximize compatibility.
- Using icon_emoji or icon_url requires the chat:write.customize scope on your bot.
- To reply in a thread, pass a valid thread_timestamp from a prior message.
- The node returns the message text, full API response, and the message timestamp for downstream use.

## Troubleshooting
- Invalid token error: Ensure slack_token is correct and the app is installed to the workspace.
- channel_not_found or not_in_channel: Verify the channel exists and the bot has access or has been invited.
- Invalid Block Kit JSON: Provide a valid JSON array of blocks; validate with Slack's Block Kit builder.
- DM resolution failed: If passing a user ID, ensure the user ID is correct and the bot has permission to open DMs.
- Permission errors (e.g., chat:write.customize): Add required scopes to the Slack app and reinstall it.
- Message not threaded: Ensure thread_timestamp is a valid Slack message ts from the target channel.
- Timeouts: Increase the hidden timeout input or check network connectivity.
