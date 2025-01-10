# Writing Node Documentation

This guide explains how to write clear, consistent documentation for Salt nodes. Our docs are built with MkDocs and hosted on GitHub Pages.

## Getting Started

### Setup
1. Clone the docs repository
2. Install dependencies:
   ```bash
   pip install mkdocs mkdocs-material
   ```
3. Preview locally:
   ```bash
   mkdocs serve
   ```

### File Structure
```
docs/
├── nodes/                 # Node documentation
│   ├── image-input.md
│   ├── text-input.md
│   └── output.md
├── images/               # Screenshots and assets
└── contributing/         # Guide documentation
```

## Documentation Structure

Every node documentation should follow this structure:

1. **Title & Description**
   * Use the node name as title (without "Node" suffix)
   * Add a brief, clear description (max 2 sentences)
   * Include node screenshot below description
   ```markdown
   # Node Name
   
   Brief description of what the node does.
   
   <img src="/images/node-name.png" alt="Node Name" class="rounded-lg">
   ```

2. **Quick Start**
   * 3-4 numbered steps to get started
   * Focus on immediate usage
   * Show where to find results
   * Include any required setup
   ```markdown
   ## Quick Start
   
   1. First step
   2. Second step with:
      * Sub-point if needed
      * Another sub-point
   3. Final step
   ```

3. **Basic Usage**
   * Show 2-3 common use cases
   * Use clear subsection headers
   * Include relevant screenshots
   * Show expected results
   ```markdown
   ## Basic Usage
   
   ### Common Use Case
   * Key point
   * Another point
   
   <img src="/images/nodes/usage-example.png" alt="Usage Example" class="rounded-lg">
   ```

4. **Configuration** (if node has inputs/outputs)
   ```markdown
   ## Configuration
   
   ### Required Inputs
   | Field | Description | Type | Example |
   |-------|-------------|------|---------|
   | Name | What it does | Type | Example |
   
   ### Optional Inputs
   ...
   ```

5. **Best Practices**
   * Include at least 2 categories
   * 3-4 actionable points per category
   * Focus on real-world usage

6. **Troubleshooting**
   * List common issues in bold
   * Provide clear solutions
   * Include where to get help

## Style Guidelines

### Writing Style
* Use active voice ("Connect the node" not "The node should be connected")
* Write directly to the user ("you" not "users" or "one")
* Keep sentences concise and clear
* Use present tense
* Avoid jargon unless necessary

### Screenshots
* Size: 800x600 maximum
* Format: PNG preferred
* Name: `node-name-context.png` (e.g., `image-input-upload.png`)
* Location: `/docs/images/nodes/`
* Include UI elements mentioned in text

### Code Examples
* Use proper syntax highlighting
* Include comments for clarity
* Show realistic examples
* Format consistently

## Common Mistakes to Avoid

* **Too Technical**: Focus on usage, not implementation
* **Missing Context**: Show where things are in the UI
* **Unclear Steps**: Number sequential actions
* **Poor Structure**: Follow the standard template
* **Outdated Screenshots**: Update when UI changes
* **Incomplete Information**: Include all required setup
* **Inconsistent Formatting**: Follow style guide

## Documentation Checklist

### Content
- [ ] Follows standard structure
- [ ] Includes node screenshot
- [ ] Has Quick Start section
- [ ] Shows Basic Usage
- [ ] Lists Best Practices
- [ ] Includes Troubleshooting

### Quality
- [ ] Uses consistent formatting
- [ ] Written in active voice
- [ ] Includes UI locations
- [ ] Shows expected results
- [ ] Screenshots are current
- [ ] Links are working

### Technical
- [ ] Images in correct directory
- [ ] Markdown renders correctly
- [ ] Tables are formatted properly
- [ ] Code blocks have language tags

## Need Help?

* Review existing node documentation
* Check the .cursorrules file
* Preview locally with `mkdocs serve`
* Ask for documentation review
* Reference this guide

## Contributing

1. Create a new branch for your changes
2. Follow the style guide and checklist
3. Test locally with MkDocs
4. Submit a pull request
5. Address any review feedback

Remember: Good documentation is crucial for user success. Take time to make it clear, accurate, and helpful. 
