# Load A3M

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads an A3M multiple sequence alignment (MSA) provided as a raw text string and labels it with a user-defined ID. Outputs a dictionary-style A3M object keyed by the provided ID, suitable for downstream biotech/protein modeling nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an A3M MSA (e.g., from external alignment tools) and need to inject it into a workflow. Provide the full A3M text and assign a unique ID so downstream nodes can reference the correct alignment.

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full A3M MSA content as plain text. Include all alignment lines exactly as produced by your MSA tool.</td><td style="word-wrap: break-word;">>query AAAAAAAAAA >homolog1 AA-A-AAA-A</td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this A3M MSA. Should be unique within the workflow. If pairing with a specific protein sequence, use a matching sequence ID for clarity.</td><td style="word-wrap: break-word;">msa</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A dictionary-like A3M object mapping the provided ID to the A3M text, ready for downstream nodes.</td><td style="word-wrap: break-word;">{"msa": ">query\nAAAAAAAAAA\n>homolog1\nAA-A-AAA-A"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unique IDs**: Ensure a3m_id values are unique across the workflow to avoid collisions when combining or batching inputs.
- **Format integrity**: Provide the A3M in its original text format; do not modify line wrapping, headers, or gap characters.
- **Sequence pairing**: If this A3M corresponds to a specific sequence input elsewhere, align the IDs for easier tracking and compatibility.

## Troubleshooting
- **Empty or malformed A3M**: If downstream nodes fail, verify that a3m_string is non-empty and follows valid A3M formatting (headers and aligned sequences).
- **ID conflicts**: If later nodes report duplicate keys or unexpected merges, change a3m_id to a unique value.
- **Unexpected downstream behavior**: Confirm that the A3M corresponds to the intended sequence (e.g., matching query sequence) to prevent misalignment issues.
