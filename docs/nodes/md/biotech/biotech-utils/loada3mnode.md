# Load A3M

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads an A3M multiple sequence alignment (MSA) from a raw text string and assigns it a user-defined ID. The node outputs a typed A3M object as a dictionary mapping the provided ID to the A3M content, enabling downstream biotech/modeling nodes to consume the MSA.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an A3M MSA string and need to feed it into biotech or protein-modeling workflows. Paste your A3M text, set a unique ID (e.g., matching the corresponding sequence ID if applicable), and connect the output to nodes that require A3M input or to combination nodes like A3M Combiner.

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full A3M MSA content as a text string. Supports multiline input.</td><td style="word-wrap: break-word;">>query/1-100 ACDEFGHIKLMNPQRSTVWY >homolog1/1-100 ACD-FGHIKL-NPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this A3M MSA. Should be unique within the workflow and ideally match the related sequence ID if you will pair it with a specific sequence.</td><td style="word-wrap: break-word;">msa</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A typed A3M object represented as a dictionary {a3m_id: a3m_string}. Suitable for downstream nodes that accept A3M input.</td><td style="word-wrap: break-word;">{'msa': '>query/1-100\nACDEFGHIKLMNPQRSTVWY'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- IDs should be unique across the workflow to avoid collisions when merging or batching data.
- If you plan to pair this MSA with a specific protein sequence or structure, ensure the a3m_id matches the corresponding sequence/chain identifier.
- Input must be valid A3M format; malformed headers or alignment lines may cause downstream failures.
- The output is a dictionary keyed by your provided ID, allowing easy combination with nodes like A3M Combiner.

## Troubleshooting
- A3M not recognized downstream: Verify the a3m_string is valid A3M format and not empty.
- ID conflict in later nodes: Ensure each a3m_id is unique within the workflow.
- Downstream node says missing chain/sequence: Align the a3m_id with the expected sequence/chain identifier used by related nodes.
- Strange characters or spacing errors: Remove unsupported characters and ensure each sequence line and header follows A3M conventions.
