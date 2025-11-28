# CIF To PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts one or more macromolecular CIF (mmCIF) structures into PDB format. Accepts a dictionary of CIF name-to-content mappings and returns a dictionary of PDB name-to-content mappings, preserving the original keys.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/ciftopdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have structures in mmCIF format and need PDB format for downstream tools or visualization. Typically placed after a loader or generator that outputs CIF and before nodes that expect PDB input.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">cif</td><td>True</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary of CIF structures to convert. Keys are structure identifiers (e.g., filenames without extension), and values are the full mmCIF text content.</td><td style="word-wrap: break-word;">{'1abc': '<full mmCIF text content>', 'complex_A': '<full mmCIF text content>'}</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of converted PDB structures. Keys mirror the input CIF keys; values are the full PDB text content.</td><td style="word-wrap: break-word;">{'1abc': '<full PDB text content>', 'complex_A': '<full PDB text content>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input must be a non-empty dictionary mapping names to valid mmCIF text content.
- **Key preservation**: Output keys match the input keys for easy alignment with upstream and downstream nodes.
- **Conversion constraints**: Invalid or malformed CIF content will cause the node to raise an error for that entry.
- **Batch behavior**: Processes all provided entries and returns all successful conversions; the node fails if none can be converted.

## Troubleshooting
- **Error: 'CIF input must be a non-empty dictionary'**: Ensure you pass a dictionary with at least one entry. The keys should be strings and values should be the full mmCIF text.
- **Error converting a specific entry**: Verify the mmCIF content is complete and well-formed. Try validating the CIF or removing non-standard or corrupted lines.
- **Empty output for some keys**: The node skips entries with empty or whitespace-only CIF content. Confirm your source provides actual structure text.
- **Memory or performance issues on large files**: Convert in smaller batches or reduce input size if encountering resource constraints.
