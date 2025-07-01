# Save To Bucket

Uploads all connected inputs to a specified folder path in a GCP bucket, applying a merge strategy for results from multiple runs. Returns a temporary download link for a zip containing the current state of the folder.

<img src="/images/nodes/biotech/biotech-utils/save-to-bucket-1.png" alt="Save To Bucket" class="rounded-lg">

<img src="/images/nodes/biotech/biotech-utils/save-to-bucket-2.png" alt="Save To Bucket" class="rounded-lg">

<img src="/images/nodes/biotech/biotech-utils/save-to-bucket-3.png" alt="Save To Bucket" class="rounded-lg">

## Quick Start

1. Connect outputs from any nodes you wish to save.
2. Specify a unique `folder_path` for your experiment in the bucket.
3. Run the node to upload and merge results.
4. Connect the output URL to an Output node to download the zip snapshot.

## Setup Guide

### 1. Connect Inputs
1. Attach outputs from any nodes (PDBs, FASTAs, CSVs, etc.) to the `results` inputs.
2. Add more results by connecting to additional `results_i` slots as needed.

### 2. Configure Folder Path
1. Set `folder_path` to a meaningful, unique path (e.g., `project1/experimentA`).
2. Avoid generic names like `test` to prevent accidental data merging.

### 3. Run and Download
1. Execute the workflow.
2. Use the output URL to download the merged results as a zip file.

## Basic Usage

### Uploading Results to GCP Bucket
* Save any combination of node outputs (PDB, FASTA, CSV, JSON, etc.).
* Specify a bucket folder path to organize results.
* Automatically merges results from multiple runs using unique execution IDs.
* Download a zip snapshot of the current folder state.

## Configuration

### Required Inputs
| Field        | Description                                                                 | Type   | Example                |
|--------------|-----------------------------------------------------------------------------|--------|------------------------|
| results_i    | Output from any node to save (add more by connecting to results_2, etc.)    | ANY    | PDB, FASTA, CSV, JSON  |
| folder_path  | Path to the output folder in GCP bucket. Supports nested paths.             | STRING | project1/experimentA   |

### Optional Inputs
*None*

### Outputs
| Field | Description                                                                                 | Example                                      |
|-------|---------------------------------------------------------------------------------------------|----------------------------------------------|
| url   | Temporary download link for a zip snapshot of the current folder state in the bucket.       | https://storage.googleapis.com/.../file.zip  |

## Best Practices

### Folder Organization
* Use descriptive, unique folder paths to avoid accidental data merging.
* Group related experiments under a common parent folder for easier management.

### Data Management
* Download the zip after each run to keep local backups.
* Regularly review bucket contents to avoid clutter and confusion.

## Troubleshooting

### Common Issues
* **Empty folder_path error**: Ensure `folder_path` is not empty or generic. Use a unique, descriptive path.
* **Unexpected data merging**: Results from different runs with the same `folder_path` are merged. Use unique paths for separate experiments.

### Need Help?
* [GCP Storage Documentation](https://cloud.google.com/storage/docs/)
* Contact support for further assistance.
