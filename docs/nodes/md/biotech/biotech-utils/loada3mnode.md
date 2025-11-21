# Load A3M MSA

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads an A3M-formatted multiple sequence alignment (MSA) string and assigns it a user-defined ID. Produces an A3M object that can be routed to downstream protein modeling or analysis nodes. The node is designed for manual paste/upload of an A3M text block and simple identification via a unique label.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an A3M MSA (e.g., generated externally) and want to inject it into a workflow for structure prediction or MSA-based analysis. Provide the full A3M text and a unique identifier. Connect its output to nodes that consume MSAs for folding, scoring, or visualization.

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full A3M MSA content as plain text. Should follow A3M format conventions (aligned sequences, possible lowercase insertions, and header lines beginning with '>').</td><td style="word-wrap: break-word;">>query MSEQNNTEMTFQIQRIYTKDIS...  >homolog_1 MSEQNN-E-TFQIQRIYTKDI... >homolog_2 MSEqnnteMTFQI-...</td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this A3M. Must be unique within the workflow. If paired with a specific protein sequence elsewhere in the workflow, use the matching sequence ID.</td><td style="word-wrap: break-word;">msa</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A3M object containing the provided MSA, keyed by the given a3m_id. Intended for downstream nodes that accept MSAs.</td><td style="word-wrap: break-word;">{'msa': 'A3M text content here...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- IDs should be unique across the workflow to avoid collisions.
- If you provide a corresponding protein sequence elsewhere in the workflow, ensure the IDs match for correct alignment pairing.
- The node does not modify or validate the biological correctness of the A3M content; ensure the format is valid.

## Troubleshooting
- A3M not recognized downstream: Verify the text follows A3M format (headers starting with '>' and aligned sequences).
- Downstream node reports missing or mismatched ID: Ensure a3m_id matches the expected sequence or workflow ID and is unique.
- Unexpected behavior with multiple MSAs: Confirm each Load A3M node uses a distinct a3m_id.
