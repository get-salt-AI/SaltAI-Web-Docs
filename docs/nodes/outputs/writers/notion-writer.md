# Notion Writer

Create and update pages in Notion databases and workspaces through the Notion API.

<img src="/images/nodes/outputs/writers/notion-writer.png" alt="Notion Writer Node" class="rounded-lg">

## Quick Start

1. [Create a Notion Integration](https://www.notion.so/my-integrations)
2. Copy your Integration Token
3. Share your database/page with the integration
4. Add the Notion Writer node to your workflow
5. Paste your token and database ID

## Setup Guide

### 1. Create a Notion Integration
1. Go to [Notion Integrations page](https://www.notion.so/my-integrations)
2. Click "New integration"
3. Name your integration
4. Select the workspace where you'll use it
5. Set capabilities (needs Content Capabilities)

### 2. Configure Permissions
1. Copy your "Internal Integration Token"
2. Go to the Notion page/database you want to write to
3. Click "•••" → "Add connections"
4. Find and select your integration

### 3. Get Parent ID
1. Open your target database/page in browser
2. Copy the ID from the URL:
   * Database: `https://notion.so/workspace/[DATABASE_ID]?v=...`
   * Page: `https://notion.so/workspace/[PAGE_ID]`

## Basic Usage

### Create Page in Database
```json
{
  "parent_type": "database",
  "parent_id": "8a5c9e1b2d3f4g5h6i7j8k9l",
  "title": "Meeting Notes",
  "content": "# Team Meeting\n\n## Agenda\n- Project updates\n- Next steps"
}
```

### Create Subpage
```json
{
  "parent_type": "page",
  "parent_id": "1a2b3c4d5e6f7g8h9i0j",
  "title": "Project Documentation",
  "content": "# Overview\n\nThis document covers..."
}
```

### Page with Cover Image
```json
{
  "parent_type": "database",
  "parent_id": "8a5c9e1b2d3f4g5h6i7j8k9l",
  "title": "Product Launch",
  "content": "# Launch Plan\n\n## Timeline...",
  "cover_url": "https://example.com/launch-banner.jpg"
}
```

## Configuration

### Required Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| **Token** | Notion Integration Token | Password | `secret_abc123...` |
| **Parent Type** | Where to create content | Select | `database` or `page` |
| **Parent ID** | Target database/page ID | String | `8a5c9e1b2d3f...` |
| **Title** | Page title | String | `Meeting Notes` |
| **Content** | Markdown content | Text | `# Heading\n\nContent...` |

### Optional Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| **Cover URL** | Page cover image | String | `https://example.com/image.jpg` |

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| **Status** | Operation result | `Success` or `Error` |
| **Response** | API response with URL | `{"url": "https://notion.so/..."}` |

## Best Practices

### Content Structure
* Use markdown for formatting
* Include clear headings
* Structure content hierarchically

### Organization
* Use consistent naming
* Leverage database properties
* Keep page structures consistent

### Performance & Security
* Store tokens securely
* Verify integration permissions
* Monitor API rate limits

## Troubleshooting

### Common Issues
* **Access Denied**: Check integration permissions
* **Invalid Token**: Verify token is correct
* **Parent Not Found**: Confirm ID and sharing settings

### Need Help?
* Test markdown in [Notion's Web App](https://notion.so)
* Check [Notion API Status](https://status.notion.so)
* Review [Notion API Documentation](https://developers.notion.com)
