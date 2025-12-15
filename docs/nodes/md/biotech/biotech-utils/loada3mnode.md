# Load A3M

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads an A3M multiple sequence alignment (MSA) string and assigns it an ID. Produces a structured MSA object where the provided ID maps to the raw A3M content. Designed to make A3M MSAs available to downstream biotech nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a workflow when you have an A3M MSA (as plain text) that you want to feed into alignment- or structure-related processing steps. Provide the full A3M content and a unique ID so it can be referenced and combined with sequences or structures later in the pipeline.

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full A3M-formatted MSA content as a text string. Should follow standard A3M conventions (headers beginning with '>', sequences may include lowercase insertions).</td><td style="word-wrap: break-word;">>query ACDEFGHIKLMNPQRSTVWY >homolog1 ACDEfGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A unique identifier to assign to this A3M MSA. This ID is used as the key in the output object and should match corresponding sequence IDs if you plan to pair with a protein sequence.</td><td style="word-wrap: break-word;">msa1</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">An A3M object represented as a dictionary-like mapping {a3m_id: a3m_string}. Use this output as the MSA input for downstream biotech nodes.</td><td style="word-wrap: break-word;">{"msa1": ">query\nACDEFGHIKLMNPQRSTVWY\n>homolog1\nACDEfGHIKLMNPQRSTVWY"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **IDs must be unique**: If you load multiple A3M files within the same workflow, assign distinct a3m_id values to avoid overwriting.
- **ID consistency**: When pairing this MSA with a protein sequence or other resources, use matching IDs so downstream nodes can correctly align inputs.
- **A3M formatting**: Ensure the input strictly follows A3M format (headers start with '>', sequences in standard format; insertions typically lowercase).
- **Large inputs**: Very large A3M strings may impact performance; consider trimming or compressing alignments if necessary.

## Troubleshooting
- **Output is empty or missing expected entries**: Verify that a3m_string is not empty and that a3m_id is set and unique within the workflow.
- **Downstream node cannot find matching MSA**: Ensure the a3m_id here matches the ID expected by downstream nodes or corresponding sequence IDs.
- **Format-related errors**: Check that the A3M content uses proper FASTA-like headers ('>') and that sequences are valid; remove unsupported characters.
- **Performance issues with large MSAs**: Reduce the number of sequences in the A3M or pre-filter the alignment before loading.
