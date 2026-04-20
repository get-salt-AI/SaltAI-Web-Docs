# Boltz Template Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node converts a single loaded PDB or CIF structure into a Boltz-compatible template object. It automatically detects whether the input text is PDB or CIF, wraps it under the appropriate key, and optionally sets chain mapping (`chain_id`, `template_id`) plus enforcement flags (`force`, `threshold`). The resulting template is designed to be integrated into a larger Boltz YAML configuration for structure prediction.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltztemplatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Boltz Template Builder when you want to guide Boltz predictions with an existing structure, such as a crystal structure or a homologous model. Upstream, provide a dictionary from a structure-loading node that maps a single file name to its content string in PDB or CIF format; the node will select the first entry and infer the format (CIF if the content starts with `data_` or contains `_atom_site.`, otherwise PDB). Optionally specify `chain_ids` to select which chains in the template structure are relevant, and `template_ids` to indicate how those template chains map to modeled chain IDs. You may also set `force` to enforce the template more strongly and `threshold` (in Å) to control spatial deviation allowed from the template. Downstream, connect the `templates` output into `BoltzListCombinerNode` together with sequences from nodes such as `BoltzSequenceNode` or `BoltzProteinSequenceNode`, constraints from `BoltzConstraintNode`, and properties from `BoltzPropertyNode`, and then feed the combined list into `BoltzYAMLCombinerNode` to produce the final Boltz YAML and auxiliary files. This node is particularly useful in template-based workflows, homology modeling, or when constraining complex interfaces using known structural data.

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
<tr><td style="word-wrap: break-word;">structure_content</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Dictionary mapping a single structure identifier (typically a file name) to its content string in PDB or CIF format. The node reads the first key, inspects its value, and classifies it as CIF if it starts with `data_` or contains `_atom_site.`, otherwise as PDB. The content must be non-empty text representing a valid structure file.</td><td style="word-wrap: break-word;">{ "1abc.pdb": "ATOM      1  N   MET A   1      11.104  13.207   2.345  1.00 20.00           N\nATOM      2  CA  MET A   1      12.560  13.400   2.600  1.00 20.00           C\n..." }</td></tr>
<tr><td style="word-wrap: break-word;">chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of chain IDs from the structure to be treated as templates, such as `A,B`. If set to a single ID, the output template will contain `"chain_id": "A"`; for multiple IDs, it will contain `"chain_id": ["A", "B"]`. If left empty, no `chain_id` field is added and the template applies without explicit chain selection.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">template_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of template chain identifiers that correspond one-to-one with `chain_ids` (for example, modeled chain names). If the number of entries does not match `chain_ids`, the mapping is ignored and a warning is logged. A single template ID is stored as a string; multiple IDs as a list.</td><td style="word-wrap: break-word;">H,L</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, adds `force: true` to the template object, signaling that Boltz should use a potential to enforce the template more strongly during prediction. When false, the template is treated as softer guidance. This flag does not change the structure content itself.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">threshold</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Distance threshold in Angstroms for template enforcement. Values greater than 0 result in a `threshold` field in the template object; 0.0 means no explicit distance limit. Valid range is 0.0 to 10.0 with configurable step sizes (commonly 0.1 Å).</td><td style="word-wrap: break-word;">2.5</td></tr>
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
<tr><td style="word-wrap: break-word;">templates</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single template object ready for Boltz YAML integration. The object includes a `pdb` or `cif` key with the full structure content string, and may include `chain_id` (string or list), `template_id` (string or list), `force` (boolean), and `threshold` (float) depending on the inputs. This list should be passed into a `BoltzListCombinerNode` and then into `BoltzYAMLCombinerNode` as the `templates` input.</td><td style="word-wrap: break-word;">[{ "pdb": "ATOM      1  N   MET A   1 ...", "chain_id": ["A", "B"], "template_id": ["H", "L"], "force": true, "threshold": 3.0 }]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This node performs only lightweight operations (string trimming, format detection, simple dict construction) and has negligible runtime cost compared to Boltz prediction itself.
- **Limitations**: Only the first entry in the `structure_content` dictionary is processed; additional keys are ignored. If you need multiple templates, run multiple instances of this node and combine outputs using `BoltzListCombinerNode`.
- **Limitations**: Format detection is heuristic; any content starting with `data_` or containing `_atom_site.` is treated as CIF, otherwise PDB. Non-standard or truncated files may be misclassified.
- **Behavior**: If the number of `template_ids` does not match the number of `chain_ids`, the node silently drops the `template_id` mapping and logs a warning instead of raising an error.
- **Behavior**: The node validates that structure content is non-empty; passing an empty or whitespace-only string will raise an error before any downstream processing.

## Troubleshooting
- **Error: 'Structure content is required'**: Triggered when `structure_content` is missing, `None`, or not a dictionary. Ensure you connect a loader node that outputs a dict like `{ "file.pdb": "PDB text..." }` directly into this input.
- **Error: 'Structure file content cannot be empty'**: Indicates that the selected structure content is empty or whitespace. Check that the source file is valid and that the upstream loader successfully read it.
- **Template IDs not applied**: If you expected `template_id` fields but do not see them in the output, verify that `template_ids` has the same number of comma-separated entries as `chain_ids`; otherwise, the mapping is ignored with only a warning in logs.
- **Unexpected PDB/CIF classification**: If the template is classified as PDB when it should be CIF (or vice versa), inspect the file header: CIF should start with `data_` or contain `_atom_site.`. Adjust the file or loader if needed so that detection works as intended.
