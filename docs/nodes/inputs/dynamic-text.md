# Dynamic Text

Allows users to create dynamic text prompts with up to 10 variable string inputs, which can be referenced in the main text using placeholders (activated by clicking `/`). Useful for templates and workflows requiring flexible, user-driven text composition.

<img src="/images/nodes/inputs/dynamic-text.png" alt="Dynamic Text" class="rounded-lg">

## Quick Start

1. Add the Dynamic Text node to your workflow.
2. Enter your main text, using `input_1`, `input_2`, etc. as placeholders.
3. Fill in the values for each input field as needed.
4. Use the output string in downstream nodes.

## Setup Guide

### 1. Add Node to Workflow
1. Drag the Dynamic Text node into your workflow editor.
2. Connect it to other nodes as required.

### 2. Configure Inputs
1. In the text area, write your template using `input_n` placeholders. Placeholders can be added by clicking on `/` and selecting the required placeholder.
2. Set values for each input field. Additional fields appear as you fill previous ones.

## Basic Usage

### Template Prompting
* Use for prompts that require user-supplied variables.
* Supports up to 10 dynamic string inputs.
* Placeholders are replaced with input values at runtime.

## Configuration

### Required Inputs
| Field    | Description                                 | Type   | Example                |
|----------|---------------------------------------------|--------|------------------------|
| text     | Main text with placeholders for variables    | STRING | "Hello, {{input_1}}!"  |

### Optional Inputs
| Field    | Description                       | Type   | Example   |
|----------|-----------------------------------|--------|-----------|
| input_1  | Value for {{input_1}} placeholder | STRING | "World"   |
| input_2  | Value for {{input_2}} placeholder | STRING | "Alice"   |
| input_3  | Value for {{input_3}} placeholder | STRING | "42"      |
| input_4  | Value for {{input_4}} placeholder | STRING | "foo"     |
| input_5  | Value for {{input_5}} placeholder | STRING | "bar"     |
| input_6  | Value for {{input_6}} placeholder | STRING | "baz"     |
| input_7  | Value for {{input_7}} placeholder | STRING | "qux"     |
| input_8  | Value for {{input_8}} placeholder | STRING | "quux"    |
| input_9  | Value for {{input_9}} placeholder | STRING | "corge"   |
| input_10 | Value for {{input_10}} placeholder| STRING | "grault"  |

### Outputs
| Field | Description                | Example                |
|-------|----------------------------|------------------------|
| text  | Final string with variables replaced | "Hello, World!" |

## Best Practices

### Template Design
* Keep templates concise for easier maintenance.

### Input Management
* Only fill as many input fields as needed; unused placeholders remain unchanged.
* Use default values for common cases to speed up workflow setup.

## Troubleshooting

### Common Issues
* **Placeholders misaplaced:** Ensure the match between `input_n` placeholder and actual input.
* **Unexpected output:** Check for typos in placeholder names and input values.

### Need Help?
* Review node configuration and input values.
* Check workflow logs for error messages.
