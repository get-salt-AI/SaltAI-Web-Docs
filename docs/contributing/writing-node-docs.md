# Writing Node Documentation

This guide explains how to write clear, consistent documentation for Salt nodes.

## Documentation Structure

Every node documentation should follow this structure:

1. **Title & Description**
   * Use the node name as title (without "Node" suffix)
   * Add a brief, clear description of the node's purpose
   * Include node screenshot below description

2. **Quick Start**
   * 3-4 numbered steps to get started
   * Focus on immediate usage
   * Show where to find results

3. **Basic Usage**
   * 2-3 common use cases as subsections
   * Use bullet points for key features
   * Include relevant screenshots

4. **Configuration** (if node has inputs/outputs)
   * Use tables for inputs and outputs
   * Include field name, description, type, example
   * Group by required and optional

5. **Best Practices**
   * At least 2 categories
   * 3-4 bullet points per category
   * Focus on practical tips

6. **Troubleshooting**
   * Common issues in bold with solutions
   * Help resources and next steps
   * Keep solutions actionable

## Style Guidelines

### Writing Style
* Use active voice ("Connect the node" not "The node should be connected")
* Write directly to the user ("you" not "users" or "one")
* Keep sentences concise and clear
* Use present tense

### Formatting
* Use proper heading hierarchy (# for title, ## for sections, ### for subsections)
* Use bullet points for lists (except numbered steps)
* Include code blocks with proper syntax highlighting
* Add screenshots for UI elements

### Examples
```markdown
# Image Input

Upload and preview images in your workflow. Perfect for starting image-based operations.

<img src="/images/nodes/image-input.png" alt="Image Input Node" class="rounded-lg">

## Quick Start

1. Add Image Input node
2. Click "Choose file to upload"
3. Preview your image
4. Connect to other nodes
```

## Best Practices

### Content Organization
* Put most important information first
* Use consistent section ordering
* Include relevant screenshots
* Link to related documentation

### User Focus
* Write from user's perspective
* Explain what they'll see
* Show where to find things
* Include troubleshooting tips

## Common Mistakes to Avoid

* **Too Technical**: Focus on usage, not implementation
* **Missing Context**: Show where things are in the UI
* **Unclear Steps**: Number sequential actions
* **Poor Structure**: Follow the standard template

## Documentation Checklist

- [ ] Follows standard structure
- [ ] Includes node screenshot
- [ ] Has Quick Start section
- [ ] Shows Basic Usage
- [ ] Lists Best Practices
- [ ] Includes Troubleshooting
- [ ] Uses consistent formatting
- [ ] Written in active voice
- [ ] Includes UI locations
- [ ] Shows expected results

## Need Help?

* Review existing node documentation
* Check the .cursorrules file
* Ask for documentation review
* Reference this guide 
