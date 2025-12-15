# Slack Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sends a message to Slack using a bot token. Supports plain text or rich Block Kit JSON with automatic fallback text for clients that don’t render blocks. Can post to channels, conversations, or open a direct message thread when a user ID is provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/outputs/writers/slackwriter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to post notifications, updates, or rich messages to Slack from your Salt pipelines. Provide a bot token, choose a destination (channel name, channel ID, or user ID), and optionally supply Block Kit JSON for rich formatting or a thread timestamp to reply in a thread.

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
<tr><td style="word-wrap: break-word;">slack_token</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Slack Bot OAuth token used to authenticate requests. Must include scopes such as chat:write and, if customizing icons, chat:write.customize.</td><td style="word-wrap: break-word;"><slack-bot-token></td></tr>
<tr><td style="word-wrap: break-word;">channel</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Destination in Slack. Accepts channel name (e.g., #general), channel ID (e.g., C0123456789), or user ID (e.g., U0123456789). If a user ID is supplied, the node opens a DM and posts there.</td><td style="word-wrap: break-word;">#general</td></tr>
<tr><td style="word-wrap: break-word;">icon_emoji</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Emoji (e.g., :robot_face:) to use as the bot icon. Requires chat:write.customize permission. Leave empty to use the app’s default icon.</td><td style="word-wrap: break-word;">:robot_face:</td></tr>
<tr><td style="word-wrap: break-word;">icon_url</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">URL of a custom bot icon (512x512 or smaller). Requires chat:write.customize permission. Ignored if icon_emoji is set.</td><td style="word-wrap: break-word;">https://example.com/icon.png</td></tr>
<tr><td style="word-wrap: break-word;">thread_timestamp</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Slack message timestamp to reply in a thread (e.g., 1734479286.246919). Leave empty to start a new top-level message.</td><td style="word-wrap: break-word;">1734479286.246919</td></tr>
<tr><td style="word-wrap: break-word;">message</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Plain text content of the message. If Block Kit JSON is provided, this is used as a fallback text (auto-generated if omitted).</td><td style="word-wrap: break-word;">Hello from Salt AI!</td></tr>
<tr><td style="word-wrap: break-word;">block_kit_json</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array of Slack Block Kit objects for rich formatting. Must be valid JSON and an array of block objects.</td><td style="word-wrap: break-word;">[{"type":"section","text":{"type":"mrkdwn","text":"Hello from Salt AI!"}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text content of the posted Slack message (final message text).</td><td style="word-wrap: break-word;">Hello from Salt AI!</td></tr>
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw Slack API response serialized as a JSON string.</td><td style="word-wrap: break-word;">{"ok": true, "channel": "C0123456789", "ts": "1734479286.246919", "message": {"text": "Hello from Salt AI!"}}</td></tr>
<tr><td style="word-wrap: break-word;">thread_timestamp</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Slack message timestamp (ts) of the posted message, useful for threading subsequent replies.</td><td style="word-wrap: break-word;">1734479286.246919</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Authentication**: A valid Slack Bot OAuth token is required.
- **Scopes**: chat:write is required; chat:write.customize is needed to use icon_emoji or icon_url.
- **Destinations**: Provide #channel, C-channelID, or U-userID. When using a user ID, the node will open a DM with that user and post there.
- **Block Kit**: block_kit_json must be a JSON array of blocks. If provided, a fallback text is auto-generated from the blocks for clients that can’t render blocks.
- **Threading**: Provide thread_timestamp to reply within an existing thread. Omit it to post a top-level message.
- **Error Redaction**: Any error messages will redact the provided token for safety.
- **Hidden Timeout**: A hidden timeout parameter controls the Slack SDK request timeout (default 60s).

## Troubleshooting
- **Invalid token or missing scopes**: Ensure the token is correct and the app has chat:write (and chat:write.customize if customizing icons). Reinstall the app if scopes changed.
- **Cannot post to channel**: Verify the bot is a member of the channel and that the channel/ID is correct. For private channels, invite the bot.
- **DM not delivered**: If using a user ID, ensure the bot can open a DM with that user and that the user has not disabled DMs from the app.
- **Invalid Block Kit JSON**: Ensure block_kit_json is valid JSON and a top-level array of block objects. Validate using Slack’s Block Kit Builder.
- **Thread timestamp rejected**: Confirm the thread_timestamp corresponds to a valid message in the target channel and is properly formatted.
- **Rate limits or timeouts**: Reduce message frequency, increase the hidden timeout, or implement retries with backoff outside this node.
