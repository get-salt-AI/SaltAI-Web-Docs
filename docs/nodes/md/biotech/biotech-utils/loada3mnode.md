# Load A3M

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a Multiple Sequence Alignment (MSA) in A3M format and packages it as an MSA-type output keyed by a user-specified ID. Useful for supplying curated or precomputed A3M alignments to downstream protein analysis and structure prediction nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an A3M MSA string and need to introduce it into a workflow. Provide the raw A3M text and assign a unique identifier. The output can be connected to nodes that consume MSA inputs (e.g., structure prediction or A3M combiner utilities).

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The A3M-formatted MSA content to upload. Should be a valid alignment in A3M format.</td><td style="word-wrap: break-word;">>query/1-100 ACDEFGHIKLM- >seq2/1-100 ACDE-GHIKLM-</td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this A3M MSA. Must be unique within the workflow. If a corresponding protein sequence is used elsewhere, its sequence ID should match this value.</td><td style="word-wrap: break-word;">msa</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">An MSA object containing the provided A3M content keyed by the given a3m_id.</td><td style="word-wrap: break-word;">{'msa': '<A3M file content here>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- IDs should be unique across the workflow to prevent collisions when combining or batching MSAs.
- Provide a valid A3M alignment; malformed content may cause downstream node failures.
- If pairing with a separate protein sequence input, align the a3m_id with that sequence’s ID for consistent mapping.
- This node outputs an MSA-typed object expected by downstream biotech nodes.

## Troubleshooting
- Duplicate ID error or unexpected merges: Ensure each a3m_id is unique across all A3M inputs.
- Downstream node rejects input: Verify the A3M text is correctly formatted and non-empty.
- Mismatch between sequence and MSA: Confirm the a3m_id matches the sequence ID used elsewhere in the workflow.
- Empty or whitespace-only input: Provide a non-empty A3M string; empty inputs are not valid.
