# Regex Search and Replace

Performs regex-based search and replace on input text, or extracts all matches using a regular expression pattern.

<img src="/images/nodes/utilities/regex-search-and-replace.png" alt="Regex Search and Replace" class="rounded-lg">

## Quick Start

1. Add the Regex Search and Replace node to your workflow.
2. Enter the input text and regex pattern.
3. Specify replacement text (for replace) or leave blank (for match).
4. Run the node to get replaced text or a list of matches.

## Setup Guide

### 1. Add Node to Workflow
1. Open your node editor.
2. Drag the Regex Search and Replace node into your workspace.

### 2. Configure Inputs
1. Enter the text to process.
2. Provide a valid regex pattern.
3. (For replace) Enter the replacement text.

## Basic Usage

### Find and Replace
* Replace all matches of a regex pattern in a string.
* Example: Replace all 5-letter words with "#####".

### Find Matches
* Extract all substrings matching a regex pattern.
* Example: Find all 6-letter words in a paragraph.

## Configuration

### Required Inputs
| Field           | Description                                 | Type   | Example                |
|-----------------|---------------------------------------------|--------|------------------------|
| text_input      | The input text to process                   | STRING | "Hello world"         |
| regex_pattern   | Regular expression pattern to match         | STRING | "\\b\\w{5}\\b"         |
| replacement_text| Text to replace each match (replace only)   | STRING | "#####"                |

### Optional Inputs
*None*

### Outputs
| Field         | Description                                 | Example                      |
|---------------|---------------------------------------------|------------------------------|
| replaced_text | Text after replacements (replace node)      | "##### world"                |
| matches       | List of all matches (match node)            | ["sample", "string"]         |

## Best Practices

### Regex Patterns
* Test your regex pattern before use.
* Use raw strings (prefix with r) to avoid escape issues.

### Usage Tips
* For complex replacements, verify the output with sample data.
* Use specific patterns to avoid unintended matches.

## Troubleshooting

### Common Issues
* **No matches found**: Check your regex pattern for accuracy.
* **Unexpected replacements**: Ensure the pattern does not match unintended text.

### Need Help?
* Refer to Python's `re` module documentation for regex syntax.
* Use online regex testers to validate your patterns.
