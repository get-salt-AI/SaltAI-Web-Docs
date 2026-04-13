# A3M Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five A3M MSA dictionaries into a single unified A3M dictionary. It ensures all chain identifiers are unique by optionally prefixing them per input and, if necessary, appending numeric suffixes to resolve remaining conflicts. A3M content itself is not altered, only reorganized under unique keys.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/a3mcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple A3M MSA inputs (for example, generated from different tools, runs, or chain groupings) and need a single merged A3M dictionary for downstream structure prediction or analysis. Connect any subset of the inputs a3m_1 through a3m_5; at least one must be a valid A3M dictionary. Enable prefix_chains when you want systematic, input-based prefixes (A3M1_, A3M2_, etc.) to avoid or clarify chain ID collisions between inputs.

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
<tr><td style="word-wrap: break-word;">a3m_1</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">First A3M MSA dictionary to merge. Keys are chain IDs (strings), values are the corresponding A3M content for each chain.</td><td style="word-wrap: break-word;">{ "A": "<a3m-content-for-chain-A>" }</td></tr>
<tr><td style="word-wrap: break-word;">a3m_2</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Second A3M MSA dictionary to merge into the combined output.</td><td style="word-wrap: break-word;">{ "B": "<a3m-content-for-chain-B>" }</td></tr>
<tr><td style="word-wrap: break-word;">a3m_3</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Third A3M MSA dictionary to merge. May contain chain IDs overlapping with other inputs.</td><td style="word-wrap: break-word;">{ "A": "<a3m-content-for-another-chain-A>" }</td></tr>
<tr><td style="word-wrap: break-word;">a3m_4</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Fourth A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{ "C": "<a3m-content-for-chain-C>" }</td></tr>
<tr><td style="word-wrap: break-word;">a3m_5</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Fifth A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{ "D": "<a3m-content-for-chain-D>" }</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes chain IDs from each input with A3M1_, A3M2_, ... according to which input slot they came from, helping systematically avoid or clarify chain ID conflicts. If duplicates still occur even after prefixing, numeric suffixes (_1, _2, ...) are added.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">A single A3M MSA dictionary that aggregates all chains from the provided inputs under unique chain IDs. Chain IDs may be prefixed and/or suffixed to prevent collisions; A3M content per chain is unchanged.</td><td style="word-wrap: break-word;">{ "A3M1_A": "<a3m-content-for-chain-A>", "A3M2_B": "<a3m-content-for-chain-B>", "A3M3_A_1": "<a3m-content-for-duplicate-A-from-3rd-input>" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: At least one valid, non-empty A3M dictionary must be provided on a3m_1–a3m_5; otherwise the node raises an error.
- **Note 2**: Inputs must be dictionaries mapping chain ID strings to A3M content strings. Non-dictionary inputs are ignored and do not contribute to the merged output.
- **Note 3**: When prefix_chains is disabled and two or more inputs share the same chain ID, conflicts are resolved by appending numeric suffixes (_1, _2, ...) to create unique keys.
- **Note 4**: When prefix_chains is enabled, each input’s chains are first prefixed with A3M1_, A3M2_, etc., based on which input slot they came from. If collisions still occur, numeric suffixes are added on top of the prefixes.
- **Note 5**: The node does not modify or concatenate A3M sequence content; it only rekeys and aggregates existing entries into a unified dictionary.
- **Note 6**: The order of inputs (a3m_1 through a3m_5) directly determines the numeric part of the A3M#_ prefix when prefix_chains is enabled, which can be useful for tracing chain provenance.

## Troubleshooting
- **Issue 1**: Error message 'At least one A3M input is required' — Ensure at least one of a3m_1–a3m_5 is connected to a valid, non-empty A3M dictionary.
- **Issue 2**: Unexpected chain ID renaming — This is usually due to chain ID collisions. Enable prefix_chains to systematically separate chains by input (A3M1_, A3M2_, etc.), and remember that numeric suffixes are added when necessary.
- **Issue 3**: Missing chains in the combined output — Verify that each connected a3m_n is actually a dictionary with string keys and A3M content values. Non-dictionary or empty inputs are ignored and will not appear in combined_a3m.
- **Issue 4**: Confusing or overly long chain IDs — Check which input slot each A3M was connected to, especially when prefix_chains is enabled, because the slot index defines the A3M#_ prefix. If desired, simplify or standardize chain IDs upstream before combining.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/a3mcombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

