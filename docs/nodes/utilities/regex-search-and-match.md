# Regex Search and Match

Performs a regular expression search on input text and returns all matches as a list.

<img src="/images/nodes/utilities/regex-search-and-match.png" alt="Regex Search and Match" class="rounded-lg">

## Quick Start

1. Add the Regex Search and Match node to your workflow.
2. Enter the text to search in the "text_input" field.
3. Provide a valid regular expression in the "regex_pattern" field.
4. Run the node to retrieve all matches.

## Setup Guide

### 1. Add Node to Workflow
1. Open your node editor.
2. Drag the Regex Search and Match node into your workspace.

### 2. Configure Inputs
1. Enter the text you want to search.
2. Specify the regex pattern for matching.

## Basic Usage

### Extracting Words
* Find all words matching a specific pattern (e.g., all 6-letter words).
* Use for text analysis, validation, or extraction tasks.

## Configuration

### Required Inputs
| Field         | Description                                 | Type   | Example                |
|--------------|---------------------------------------------|--------|------------------------|
| text_input   | The input text to search for matches        | STRING | "Find all words here" |
| regex_pattern| Regular expression pattern to match         | STRING | "\\b[a-zA-Z]{6}\\b"   |

### Optional Inputs
*None*

### Outputs
| Field   | Description                        | Example                |
|---------|------------------------------------|------------------------|
| matches | List of all regex matches found     | ["search", "result"]  |

## Best Practices

### Regex Construction
* Test your regex patterns before use.
* Use raw strings (prefix with r) to avoid escape issues.

### Performance
* Use specific patterns to avoid excessive backtracking.
* Limit input size for large-scale searches.

## Troubleshooting

### Common Issues
* **No matches found**: Check your regex pattern and input text for correctness.
* **Invalid regex error**: Ensure your pattern is valid and properly escaped.

### Need Help?
* Refer to Python's `re` module documentation for regex syntax and examples.
