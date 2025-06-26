# Output

View and save results from your workflow. The Output node automatically formats and displays your data in a readable way.

<img src="/images/nodes/outputs/output.png" alt="Output Node" class="rounded-lg">

## Quick Start

1. Add Output node to your workflow
2. Connect any node's output to it
3. Run your workflow
4. View results in:
   * Node preview (toggle raw/formatted views)
   * Workflow Runs panel on the right (shows history)

## Basic Usage

### Preview Mode
* Shows formatted content (like rendered Markdown)
* Great for reviewing reports and text
* Automatically detects and displays images

### Raw Mode
* Shows underlying data structure
* Useful for debugging
* Displays exact values being passed

### Saving Ouptuts

<img src="/images/workflow-runs.png" alt="Workflow Runs Panel" class="rounded-lg">

* All outputs are viewable in the Workflow Runs panel
* Each run shows timestamp and duration
* Access historical results anytime

## Best Practices

### Workflow Organization
* Add Output nodes to inspect intermediate results
* Use multiple outputs to compare different stages
* Label outputs clearly for better workflow readability

### Viewing Results
* Use preview mode for formatted content
* Switch to raw mode to verify data structure
* Check Workflow Runs panel for previous results

## Troubleshooting

### Common Issues
* **Empty Output**: Check input connection
* **Preview Not Loading**: Try switching to raw view
* **Unexpected Format**: Verify input data type

### Need Help?
* Verify node connections
* Check Workflow Runs history
* Try both preview and raw modes
