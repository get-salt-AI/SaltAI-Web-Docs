# Load A3M

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads an A3M-formatted multiple sequence alignment (MSA) string and assigns it a user-defined identifier. Produces a typed A3M output as a dictionary mapping the given ID to the provided A3M content.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a protein design or structure prediction workflow to import an existing MSA in A3M format. Provide the raw A3M text and an ID for referencing it downstream. Connect its output to nodes that consume A3M (e.g., MSA combiners or structure prediction steps).

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The raw A3M MSA content as a text string. Should follow A3M conventions (FASTA-like headers, aligned sequences, insertions typically in lowercase).</td><td style="word-wrap: break-word;"><a3m-formatted-msa></td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this A3M entry. Must be unique within the workflow if multiple A3Ms are used, and should match related sequence IDs when applicable.</td><td style="word-wrap: break-word;">msa</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A typed A3M object represented as a dictionary mapping the provided ID to the A3M string.</td><td style="word-wrap: break-word;">{'msa': '<a3m-formatted-msa>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- IDs should be unique if you load multiple A3M files in the same workflow to avoid collisions.
- If you also provide protein sequences elsewhere in the workflow, use matching IDs to keep data aligned.
- This node does not validate A3M formatting; ensure your input follows A3M conventions to prevent downstream errors.

## Troubleshooting
- A3M content rejected downstream: Ensure the input is valid A3M (proper headers like >id and correctly aligned sequences, with insertions in lowercase).
- Downstream node reports missing MSA by ID: Confirm that a3m_id matches the expected sequence or chain ID in connected nodes.
- Duplicate ID conflict: Change a3m_id to a unique value when multiple A3Ms are loaded in the workflow.
