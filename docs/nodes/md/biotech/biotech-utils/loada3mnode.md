# Load A3M

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a Multiple Sequence Alignment (MSA) in A3M format and packages it into an MSA-typed object keyed by a user-specified ID. This enables curated or precomputed A3M alignments to be injected into biotech workflows for downstream protein analysis and structure prediction.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Load A3M when you already have an A3M-formatted MSA string and need to introduce it into a Salt biotech workflow. Provide the raw A3M text and a unique a3m_id, then connect the msa.a3m output to nodes that consume MSA inputs such as structure prediction nodes or A3M combiner utilities.

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full A3M-formatted MSA content to load. Must be valid text alignment in A3M format, including headers and aligned sequences.</td><td style="word-wrap: break-word;">>query/1-100 ACDEFGHIKLM- >seq2/1-100 ACDE-GHIKLM-</td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this A3M MSA instance. Should be unique within the workflow and, when applicable, should match the ID of a corresponding protein sequence used elsewhere.</td><td style="word-wrap: break-word;">msa</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">An MSA-typed object wrapping the provided A3M content, keyed by the specified a3m_id. This is the format expected by downstream biotech nodes that operate on MSAs.</td><td style="word-wrap: break-word;">{ "msa": "<A3M file content here>" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: Ensure a3m_id values are unique across the workflow to avoid collisions or unintended overwriting when combining multiple MSAs.
- **Note 2**: The input must be valid A3M format; malformed or empty content can cause downstream nodes to fail or reject the input.
- **Note 3**: When pairing this MSA with a separate sequence input, keep the a3m_id synchronized with the corresponding sequence ID for consistent mapping.
- **Note 4**: The output is an MSA-typed object specifically designed for use with biotech nodes that expect MSA inputs.

## Troubleshooting
- **Issue 1**: Duplicate ID collisions or unexpected merges. Resolution: Assign a unique a3m_id for each loaded A3M and verify no other node is using the same ID for a different MSA.
- **Issue 2**: Downstream node rejects or fails on this MSA. Resolution: Check that the A3M text is well-formed, non-empty, and follows standard A3M formatting (headers, alignment lines, and no corrupted characters).
- **Issue 3**: Sequence–MSA mismatch in later steps. Resolution: Confirm that the a3m_id exactly matches the sequence ID used by any sequence-loading or structure-prediction nodes.
- **Issue 4**: No effect when connecting to an MSA consumer node. Resolution: Verify that you are connecting the msa.a3m output to the corresponding MSA input on the downstream node.
