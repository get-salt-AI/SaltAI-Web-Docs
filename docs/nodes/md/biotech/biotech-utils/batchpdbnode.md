# Batch PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines multiple PDB inputs into a single batch dictionary. Each input is itself a dictionary mapping pdb_id to PDB content, and all keys must be unique across the batch. The node validates duplicate IDs and raises an error if a collision is found.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/batchpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to merge several PDB structures (from different loaders or generators) into one consolidated input for downstream biotech nodes that process batches. Connect pdb_1 first; additional inputs (pdb_2 to pdb_31) become available progressively after the previous one is connected.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB dictionary to add to the batch. Must be a dict of {pdb_id: pdb_content}.</td><td style="word-wrap: break-word;">{"protein_A": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_2</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Second PDB dictionary to merge. Appears after pdb_1 is connected.</td><td style="word-wrap: break-word;">{"protein_B": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_3</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Third PDB dictionary to merge. Appears after pdb_2 is connected.</td><td style="word-wrap: break-word;">{"protein_C": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_4</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourth PDB dictionary to merge. Appears after pdb_3 is connected.</td><td style="word-wrap: break-word;">{"protein_D": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_5</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifth PDB dictionary to merge. Appears after pdb_4 is connected.</td><td style="word-wrap: break-word;">{"protein_E": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_6</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Sixth PDB dictionary to merge. Appears after pdb_5 is connected.</td><td style="word-wrap: break-word;">{"protein_F": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_7</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Seventh PDB dictionary to merge. Appears after pdb_6 is connected.</td><td style="word-wrap: break-word;">{"protein_G": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_8</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Eighth PDB dictionary to merge. Appears after pdb_7 is connected.</td><td style="word-wrap: break-word;">{"protein_H": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_9</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ninth PDB dictionary to merge. Appears after pdb_8 is connected.</td><td style="word-wrap: break-word;">{"protein_I": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_10</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Tenth PDB dictionary to merge. Appears after pdb_9 is connected.</td><td style="word-wrap: break-word;">{"protein_J": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_11</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Eleventh PDB dictionary to merge. Appears after pdb_10 is connected.</td><td style="word-wrap: break-word;">{"protein_K": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_12</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twelfth PDB dictionary to merge. Appears after pdb_11 is connected.</td><td style="word-wrap: break-word;">{"protein_L": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_13</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Thirteenth PDB dictionary to merge. Appears after pdb_12 is connected.</td><td style="word-wrap: break-word;">{"protein_M": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_14</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourteenth PDB dictionary to merge. Appears after pdb_13 is connected.</td><td style="word-wrap: break-word;">{"protein_N": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_15</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifteenth PDB dictionary to merge. Appears after pdb_14 is connected.</td><td style="word-wrap: break-word;">{"protein_O": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_16</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Sixteenth PDB dictionary to merge. Appears after pdb_15 is connected.</td><td style="word-wrap: break-word;">{"protein_P": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_17</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Seventeenth PDB dictionary to merge. Appears after pdb_16 is connected.</td><td style="word-wrap: break-word;">{"protein_Q": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_18</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Eighteenth PDB dictionary to merge. Appears after pdb_17 is connected.</td><td style="word-wrap: break-word;">{"protein_R": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_19</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Nineteenth PDB dictionary to merge. Appears after pdb_18 is connected.</td><td style="word-wrap: break-word;">{"protein_S": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_20</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twentieth PDB dictionary to merge. Appears after pdb_19 is connected.</td><td style="word-wrap: break-word;">{"protein_T": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_21</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-first PDB dictionary to merge. Appears after pdb_20 is connected.</td><td style="word-wrap: break-word;">{"protein_U": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_22</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-second PDB dictionary to merge. Appears after pdb_21 is connected.</td><td style="word-wrap: break-word;">{"protein_V": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_23</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-third PDB dictionary to merge. Appears after pdb_22 is connected.</td><td style="word-wrap: break-word;">{"protein_W": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_24</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-fourth PDB dictionary to merge. Appears after pdb_23 is connected.</td><td style="word-wrap: break-word;">{"protein_X": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_25</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-fifth PDB dictionary to merge. Appears after pdb_24 is connected.</td><td style="word-wrap: break-word;">{"protein_Y": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_26</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-sixth PDB dictionary to merge. Appears after pdb_25 is connected.</td><td style="word-wrap: break-word;">{"protein_Z": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_27</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-seventh PDB dictionary to merge. Appears after pdb_26 is connected.</td><td style="word-wrap: break-word;">{"complex_1": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_28</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-eighth PDB dictionary to merge. Appears after pdb_27 is connected.</td><td style="word-wrap: break-word;">{"complex_2": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_29</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Twenty-ninth PDB dictionary to merge. Appears after pdb_28 is connected.</td><td style="word-wrap: break-word;">{"complex_3": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_30</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Thirtieth PDB dictionary to merge. Appears after pdb_29 is connected.</td><td style="word-wrap: break-word;">{"complex_4": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_31</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Thirty-first PDB dictionary to merge. Appears after pdb_30 is connected.</td><td style="word-wrap: break-word;">{"complex_5": "ATOM ...\nEND"}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A single merged PDB batch dictionary containing all input entries, formatted as {pdb_id: pdb_content}.</td><td style="word-wrap: break-word;">{"protein_A": "ATOM ...\nEND", "protein_B": "ATOM ...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unique IDs required**: All pdb_id keys across all inputs must be unique; duplicate IDs cause the node to raise an error.
- **Progressive inputs**: Additional inputs (pdb_2 to pdb_31) become visible only after the previous one is connected.
- **Input format**: Each input must be a dictionary mapping IDs to PDB strings; passing a raw string instead of a dict will fail.
- **Batch-friendly**: Inputs can themselves be batches (dicts with multiple entries); the node merges all provided dictionaries.

## Troubleshooting
- **Duplicate ID error**: If you see an error about an ID repeating twice, rename the conflicting pdb_id in one of the inputs before merging.
- **Nothing happens for later ports**: If pdb_2 (or later) is not visible, connect pdb_1 first; inputs appear sequentially.
- **Invalid input type**: Ensure each connected input is a PDB dictionary (e.g., output of a PDB loader/combiner) rather than a plain string.
- **Empty output**: At least pdb_1 is required; verify that each provided dictionary contains at least one entry.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/batchpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

