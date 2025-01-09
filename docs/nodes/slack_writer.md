# **Slack Writer Node Documentation**

The Slack Writer node allows users to send messages to a Slack workspace using the Slack API. This documentation provides an overview of the node's inputs. For more information on Slack Block-Kit, please refer to [**Slack's Block-Kit Documentation**](https://api.slack.com/block-kit/building).

<img src="/images/nodes/slack_writer.png" alt="Slack Writer Node" class="rounded-lg">

## Quick Facts
* Allows users to send messages to public channels, private channels, or direct messages.
* Supports advanced formatting using Slack Block-Kit for rich message content.
* Requires a Slack bot token with appropriate permissions.

## Note:
* Ensure your Slack Bot has the necessary permission scopes to send messages and customize the bot's icon. For advanced messages, refer to [**Slack API Scopes Documentation**](https://api.slack.com/scopes).


## Input
* **Slack Token**
  - **Description**: The Slack bot token required to authenticate and send messages. This token must have the necessary permissions (`chat:write`, `chat:write.customize`) in the Slack workspace. You can obtain a token from your Slack account or try the [**quick-start tutorial**](https://api.slack.com/tutorials/tracks/getting-a-token).
  - **Type**: Password (hidden string)
  - **Example Input**: `xoxb-1234567890-abcdefg-hijklmnop`
  - **Tooltip**: Slack Bot authentication token. Obtain one through the quick-start tutorial: https://api.slack.com/quickstart.

* **Channel**
  - **Description**: The Slack channel where the message will be sent. It can be a channel name or user ID.
  - **Type**: String
  - **Default Value**: `#general`
  - **Example Input**:
    - Public channel: `#general`
    - Direct user message: `U12345`
  - **Tooltip**: Specify the channel name (e.g., `#general`), channel ID (e.g., `C05NNPR6A42`), or user ID (e.g., `U031AFC238B`).

* **Icon Emoji (Optional)**
  - **Description**: Emoji to use as the bot's icon. The bot must have the `chat:write.customize` permission to use this feature. If provided, it overrides `icon_url`.
  - **Type**: String
  - **Default Value**: Empty
  - **Example Input**: `:robot:`
  - **Tooltip**: Emoji tag (e.g., `:smile:` or `:robot:`).

* **Icon URL (Optional)**
  - **Description**: URL of an image to use as the bot's icon. The image should be 512x512 pixels or smaller. The bot must have the `chat:write.customize` permission to use this feature.
  - **Type**: String
  - **Default Value**: Empty
  - **Example Input**: `https://example.com/bot-icon.png`
  - **Tooltip**: URL of a valid image to customize the bot's icon.

* **Thread Timestamp (Optional)**
  - **Description**: Timestamp of the parent message to reply to in a thread. If provided, the message will be posted as a reply. This is obtained via the API when writing a message. Use a previous Slack Writer node's `thread_timestamp` output for threading or save it for another workflow.
  - **Type**: String
  - **Default Value**: Empty
  - **Example Input**: `1680123456.789000`
  - **Tooltip**: Provide the thread timestamp to reply to a specific message.

* **Message**
  - **Description**: Plain text message content. If `block_kit_json` is provided, this input will be overridden. Useful for simple text-based messages.
  - **Type**: Multiline String
  - **Default Value**: `Hello, from Salt AI!`
  - **Placeholder**: `Insert message...`
  - **Example Input**: `Hello, team! This is a test message.`
  - **Tooltip**: The text content of the message.

* **Block Kit JSON (Optional)**
  - **Description**: JSON-formatted string for creating advanced messages using Slack Block-Kit. Overrides the `message` input if provided. Use this to send rich content like buttons, images, and interactive elements.
  - **Type**: Multiline String
  - **Default Value**:
      ```json
      [
        {
          "type": "section",
          "text": {
            "type": "mrkdwn",
            "text": "Hello from Salt AI! This is a block-kit message."
          }
        },
        {
          "type": "actions",
          "elements": [
            {
              "type": "button",
              "text": {
                "type": "plain_text",
                "text": "Check out Salt AI",
                "emoji": true
              },
              "url": "<https://getsalt.ai>"
            }
          ]
        }
      ]
      ```
  - **Example Input**:
      ```json
      [
        {
          "type": "section",
          "text": {
            "type": "mrkdwn",
            "text": "Hello from the Slack Writer Node!"
          }
        },
        {
          "type": "divider"
        },
        {
          "type": "actions",
          "elements": [
            {
              "type": "button",
              "text": {
                "type": "plain_text",
                "text": "Learn More",
                "emoji": true
              },
              "url": "<https://www.example.com>"
            }
          ]
        }
      ]
      ```
  - **Tooltip**: Block-Kit JSON for rich content. Refer to https://api.slack.com/block-kit/building.


## Output
* **Status**
  - **Description**: The status of the Slack API call. Usually `ok` if successful.
  - **Type**: String
  - **Example Output**: `ok`

* **Response**
  - **Description**: The full API response from Slack in JSON format.
  - **Type**: String
  - **Example Output**:
      ```json
      {
        "channel": "C12345678",
        "ts": "1680123456.789000",
        "message": {
          "text": "Hello, from Salt AI!",
          "user": "U12345678"
        }
      }
      ```

* **Thread Timestamp**
  - **Description**: The timestamp of the sent message, useful for replying to a previous channel message to create a thread with more information.
  - **Type**: String
  - **Example Output**: `1680123456.789000`
