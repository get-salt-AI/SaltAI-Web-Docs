# Save To Zip

Saves all connected node outputs into a downloadable zip folder, preserving folder and file structure based on node metadata. Enables selective export of results (PDBs, FASTAs, CSVs, etc.) for local download.

<img src="/images/nodes/biotech/biotech-utils/save-to-zip-1.png" alt="Save To Zip" class="rounded-lg">

<img src="/images/nodes/biotech/biotech-utils/save-to-zip-2.png" alt="Save To Zip" class="rounded-lg">

## Quick Start

1. Connect outputs from any nodes you wish to save.
2. Add additional results as needed (slots appear dynamically).
3. Connect the Save To Zip node output to the Output node.
4. Download the generated zip file via the provided URL.

## Setup Guide

### 1. Add Node to Workflow
1. Insert the Save To Zip node into your workflow.
2. Connect outputs from other nodes (e.g., PDB, FASTA, CSV).

### 2. Configure and Run
1. Add more results by connecting to new slots as they appear.
2. Run the workflow and download the zip from the Output node.

## Basic Usage

### Exporting Results
* Save any combination of node outputs (PDBs, FASTAs, CSVs, JSON, etc.)
* Folder and file names are auto-generated based on node metadata
* Download a single zip containing all selected results

## Configuration

### Required Inputs
| Field      | Description                                                      | Type   | Example         |
|------------|------------------------------------------------------------------|--------|-----------------|
| results_1  | Output from any node to save (additional slots appear as needed) | ANY    | PDB, FASTA, CSV |

### Optional Inputs
*None*

### Outputs
| Field | Description                                 | Type   | Example                |
|-------|---------------------------------------------|--------|------------------------|
| url   | URL to download the created zip file locally| STRING | /output/abc/abc.zip    |

## Best Practices

### Organizing Exports
* Connect only the outputs you need to save
* Use meaningful node names for easier folder navigation in the zip

### Workflow Integration
* Always connect the Save To Zip output to an Output node for easy download
* Use multiple Save To Zip nodes if you want to export different result sets

## Troubleshooting

### Common Issues
* **No zip file generated**: Ensure at least one result is connected.
* **Duplicate folder names**: Multiple instances of the same node will be auto-suffixed (e.g., node, node_2).
* **Missing expected files**: Check that all desired outputs are connected to the node.

### Need Help?
* Contact support for further assistance.
