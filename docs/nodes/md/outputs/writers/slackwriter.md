# Slack Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sends messages to Slack channels, users (DMs), or threads using a Slack Bot token. Supports plain-text messages and rich Block Kit payloads, auto-generating a fallback text for clients that cannot render blocks. Can customize bot icon via emoji or URL and reply in an existing thread.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/outputs/writers/slackwriter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to post notifications, updates, or interactive Block Kit messages into Slack from your workflow. Provide a Bot token, a target channel (channel name/ID or user ID for DM), and either plain text or a Block Kit JSON payload. Optionally reply within a thread using a timestamp and customize the bot appearance.

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
<tr><td style="word-wrap: break-word;">slack_token</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Slack Bot authentication token used to authorize API requests.</td><td style="word-wrap: break-word;"><slack-bot-token></td></tr>
<tr><td style="word-wrap: break-word;">channel</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Destination in Slack. Accepts a channel name (e.g., #general), a channel ID (e.g., C05NNPR6A42), or a user ID (e.g., U031AFC238B) to open a DM.</td><td style="word-wrap: break-word;">#general</td></tr>
<tr><td style="word-wrap: break-word;">icon_emoji</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Emoji to use as the bot icon (requires chat:write.customize).</td><td style="word-wrap: break-word;">:robot_face:</td></tr>
<tr><td style="word-wrap: break-word;">icon_url</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Direct URL to a bot icon image (512×512 or smaller) (requires chat:write.customize).</td><td style="word-wrap: break-word;">https://example.com/bot-icon.png</td></tr>
<tr><td style="word-wrap: break-word;">thread_timestamp</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Slack message timestamp to reply in a thread. When provided, the message is posted as a thread reply.</td><td style="word-wrap: break-word;">1734479286.246919</td></tr>
<tr><td style="word-wrap: break-word;">message</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Plain text content of the message. Ignored if block_kit_json is provided.</td><td style="word-wrap: break-word;">Hello from Salt AI!</td></tr>
<tr><td style="word-wrap: break-word;">block_kit_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array of Block Kit blocks for rich message content. If provided, it overrides the plain-text message and a fallback text is auto-generated from the blocks.</td><td style="word-wrap: break-word;">[{"type":"section","text":{"type":"mrkdwn","text":"Hello from Salt AI!"}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The final text Slack registered for the posted message (plain text or fallback from blocks).</td><td style="word-wrap: break-word;">Hello from Salt AI!</td></tr>
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw Slack API response serialized as JSON for debugging or downstream use.</td><td style="word-wrap: break-word;">{"ok": true, "channel": "C05NNPR6A42", "ts": "1734479286.246919", "message": {"text": "Hello from Salt AI!"}}</td></tr>
<tr><td style="word-wrap: break-word;">thread_timestamp</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The timestamp of the posted message (ts), useful for threading subsequent replies.</td><td style="word-wrap: break-word;">1734479286.246919</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Block Kit precedence**: If block_kit_json is provided, it overrides the message input. A fallback text is generated by extracting text fields from the blocks.
- **DM handling**: Supplying a user ID (starting with 'U') will automatically open a DM and post the message there.
- **Thread replies**: Provide a valid thread_timestamp (ts) to post as a reply in a thread.
- **Icon customization**: Using icon_emoji or icon_url requires the chat:write.customize permission on your bot.
- **Block Kit format**: The block_kit_json must be a JSON array of valid Block Kit blocks.
- **Token safety**: Errors will redact your token in logs; always use a bot token with appropriate scopes.
- **Channel access**: The bot must be in the target channel or have permission to DM the user.

## Troubleshooting
- **Invalid Block-Kit JSON**: Ensure block_kit_json is valid JSON and a top-level array of blocks. Validate using Slack's Block Kit Builder.
- **not_in_channel or channel_not_found**: Invite the bot to the channel or verify the channel ID/name.
- **invalid_auth or not_authed**: Use a valid Slack Bot token and confirm required scopes (e.g., chat:write, chat:write.customize for icon overrides).
- **thread_ts_invalid**: Verify the thread_timestamp corresponds to an existing message's ts in the target channel.
- **Cannot DM user**: Confirm the user ID starts with 'U' and that the bot is allowed to initiate DMs; retry if conversations_open fails.
- **rate_limited**: Slack may rate limit requests. Add retries or backoff and reduce message frequency.
- **Timeouts**: Increase the hidden timeout value if requests are timing out due to network conditions.
