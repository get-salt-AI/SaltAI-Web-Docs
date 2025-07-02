# File

Provides access to files uploaded to the workflow, returning both the file path and its UTF-8 contents (if text). Useful for integrating file data into node pipelines.

<img src="/images/nodes/inputs/file.png" alt="File" class="rounded-lg">

## Quick Start

1. Upload a file to the workflow input directory.
2. Add the File node to your workflow.
3. Select the desired file from the dropdown.

## Setup Guide

### 1. Upload Files
1. Place files in the workflow's input directory.
2. Ensure files are accessible and have appropriate permissions.

### 2. Add and Configure Node
1. Insert the File node into your workflow.
2. Select a file from the provided list.

## Basic Usage

### Reading File Path and Contents
* Select a file to output its path and, if text, its contents.
* Use the file path output for downstream nodes that require file references.
* Use the text contents output for nodes that process text data.

## Configuration

### Required Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
| file  | File to load from the input directory | string (filename) | "example.txt" |

### Optional Inputs
*None*

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| file_path | Path to the selected file | "/inputs/example.txt" |
| text_contents | UTF-8 contents of the file (empty for non-text files) | "Hello, world!" |

## Best Practices

### File Management
* Keep the input directory organized for easier file selection.
* Use descriptive filenames to avoid confusion.

### Data Handling
* Only use the text_contents output for supported text file types.
* For binary or unsupported files, rely on the file_path output.

## Troubleshooting

### Common Issues
* **File not listed**: Ensure the file is in the correct input directory and has proper permissions.
* **Empty text output**: The file may be binary or an unsupported format.

### Need Help?
* Check workflow logs for error messages.
* Verify file accessibility and format.
