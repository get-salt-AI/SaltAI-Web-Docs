# Slack Writer

Send messages to Slack channels and users through the Slack API.

<img src="/images/nodes/slack_writer.png" alt="Slack Writer Node" class="rounded-lg">

## Quick Start

1. [Create a Slack App](https://api.slack.com/apps) and install it to your workspace
2. Copy your Bot User OAuth Token (starts with `xoxb-`)
3. Add the Slack Writer node to your workflow
4. Paste your token and specify a channel (e.g., `#general`)
5. Send a test message

## Setup Guide

### 1. Create a Slack App
1. Go to [Slack API Apps page](https://api.slack.com/apps)
2. Click "Create New App" → "From scratch"
3. Name your app and select your workspace

### 2. Configure Bot Permissions
1. Navigate to "OAuth & Permissions"
2. Add these Bot Token Scopes:
   * `chat:write` - Send messages
   * `chat:write.customize` - Customize bot name/icon
   * `channels:read` - View channels
   * `groups:read` - View private channels

### 3. Install & Configure
1. Click "Install to Workspace" and authorize
2. Copy the "Bot User OAuth Token"
3. In Slack, invite bot to channels with `/invite @YourBotName`

## Basic Usage

### Simple Message
```json
{
  "channel": "#general",
  "message": "Hello team! Important meeting at 2 PM."
}
```

### Thread Reply
```json
{
  "channel": "#general",
  "message": "Here are the meeting notes.",
  "thread_timestamp": "1680123456.789000"
}
```

### Rich Message with Block Kit
```json
[
  {
    "type": "header",
    "text": {
      "type": "plain_text",
      "text": "🚨 System Alert",
      "emoji": true
    }
  },
  {
    "type": "section",
    "text": {
      "type": "mrkdwn",
      "text": "*Database Backup Complete*\nAll systems operating normally."
    }
  }
]
```

## Configuration

### Required Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| **Slack Token** | Bot User OAuth Token | Password | `xoxb-1234567890-abcdefg-hijklmnop` |
| **Channel** | Channel or user to message | String | `#general` or `@username` |
| **Message** | Text content (if not using Block Kit) | String | `Hello team!` |

### Optional Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| **Block Kit JSON** | Rich message formatting | JSON | See examples below |
| **Icon Emoji** | Bot icon emoji | String | `:robot:` |
| **Icon URL** | Bot icon image URL | String | `https://example.com/icon.png` |
| **Thread Timestamp** | Reply to thread | String | `1680123456.789000` |

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| **Status** | API call status | `ok` |
| **Thread Timestamp** | Message timestamp | `1680123456.789000` |
| **Response** | Full API response | See below |

## Best Practices

### Message Design
* Keep messages concise and well-structured
* Use Block Kit for complex layouts
* Include clear call-to-actions

### Threading
* Use threads to organize conversations
* Save timestamps for follow-ups
* Avoid deep nesting

### Performance & Security
* Store tokens securely
* Batch messages when possible
* Monitor rate limits

## Troubleshooting

### Common Issues
* **Message Not Sent**: Check token and channel permissions
* **Invalid Token**: Ensure token starts with `xoxb-`
* **Channel Not Found**: Verify channel exists and bot is invited

### Need Help?
* Validate Block Kit JSON in [Block Kit Builder](https://app.slack.com/block-kit-builder)
* Check [Slack API Status](https://status.slack.com/)
* Review [Slack's API Documentation](https://api.slack.com/messaging)
