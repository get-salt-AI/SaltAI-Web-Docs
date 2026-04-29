# RF Diffusion Contigmap Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a contigmap configuration object for RF Diffusion, defining which sequence and structural regions are masked or preserved and how long the designed segments may be. It supports sequence inpainting, structure inpainting (including helix/strand/loop‑specific masking), explicit unmask ranges, and overall sequence length or length range. The result is a validated configuration dictionary that plugs directly into the RF Diffusion node’s contigmap_config input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusioncontigmapconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need RF Diffusion to redesign only specific parts of a protein while keeping other regions fixed, or when you want to constrain sequence length. Typical scenarios include loop redesign around an active site, remodelling selected helices or strands, or exploring binders with variable length but fixed motifs.

Workflow position: upstream of SaaSRFDiffusionNode (RF Diffusion) as a configuration helper. You configure your main design in SaaSRFDiffusionNode (checkpoint, contigs, partial_T, etc.), and connect this node’s output to its contigmap_config input. It can be used together with SaaSRFDiffusionSymmetryConfigNode, SaaSRFDiffusionDenoiserConfigNode, SaaSRFDiffusionPotentialsConfigNode, and SaaSRFDiffusionScaffoldGuidedConfigNode to fully specify an RF Diffusion run.

Best practices: keep length empty for conditional designs based on an input PDB unless you truly want to impose a length constraint. Use consistent chain IDs and residue indices that match your input structure. Start with one or two ranges in a single field (for example, only inpaint_str_loop) to confirm behavior before composing more complex, overlapping masks.

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
<tr><td style="word-wrap: break-word;">inpaint_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Amino‑acid sequence regions to be masked and redesigned. Uses the same notation as the RF Diffusion contigs input: optional chain ID plus an inclusive residue range, with multiple segments separated by commas. An empty string means no sequence masking. Values are validated using the contig parser; malformed or out‑of‑order ranges will raise an error.</td><td style="word-wrap: break-word;">A30-45,B10-20</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue index ranges whose 3D coordinates should be masked and redesigned (structural inpainting). Use chain+range syntax (for example, B165-178) and separate multiple ranges with commas. Leave empty to avoid structure masking. Input is parsed and converted into the internal contig representation; invalid syntax or indices cause a validation error.</td><td style="word-wrap: break-word;">A85-110,B165-178</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_helix</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ranges where helix secondary structure should be masked and redesigned. Lets you remodel specific helices while leaving other regions intact. Accepts one or more inclusive ranges, optionally with chain IDs, separated by commas. Empty string means no helix‑specific masking. All values are parsed with the same contig validation logic as other fields.</td><td style="word-wrap: break-word;">A10-20,A30-40</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_strand</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ranges where beta‑strand secondary structure should be masked and redesigned. Use inclusive ranges (with or without chain IDs) separated by commas. Leave empty to keep all strands unchanged. Incorrectly formatted ranges (for example, letters in the numeric part or start greater than end) will fail validation.</td><td style="word-wrap: break-word;">B50-60,B70-80</td></tr>
<tr><td style="word-wrap: break-word;">inpaint_str_loop</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Loop regions whose structure should be masked and redesigned. Useful for loop engineering while preserving helices and strands. Accepts comma‑separated inclusive ranges, optionally with chain IDs. Empty string disables loop‑specific masking. All ranges are validated and converted into the internal contig format.</td><td style="word-wrap: break-word;">A25-29,A65-69</td></tr>
<tr><td style="word-wrap: break-word;">provide_seq</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence regions to unmask (keep fixed) by providing explicit inclusive ranges. Used when you only want certain regions to be redesigned and others to remain unchanged. Use comma‑separated ranges such as 172-177,200-205. Empty string means all eligible regions are free to be redesigned. Parsed and validated; overlapping or malformed ranges will trigger errors.</td><td style="word-wrap: break-word;">172-177,200-205</td></tr>
<tr><td style="word-wrap: break-word;">length</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Allowed total sequence length or length range for the designed segment(s). Use a single integer for fixed length (for example, 100) or min-max notation (for example, 100-150) for a range. Most relevant in unconditional designs without an input PDB. The node validates basic format but treats this as a length constraint rather than a full contig layout.</td><td style="word-wrap: break-word;">120-160</td></tr>
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
<tr><td style="word-wrap: break-word;">contigmap_config</td><td style="word-wrap: break-word;">RFDIFFUSION_CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">A contigmap configuration dictionary containing parsed and validated inpainting and length settings derived from the input strings. Empty strings are converted to null/omitted, and valid ranges become structured contig entries appropriate for the RF Diffusion backend. Connect this output directly to the contigmap_config input of SaaSRFDiffusionNode (RF Diffusion).</td><td style="word-wrap: break-word;">{"inpaint_seq": [["A", 30, 45], ["B", 10, 20]], "inpaint_str": [["A", 85, 110], ["B", 165, 178]], "inpaint_str_helix": [["A", 10, 20], ["A", 30, 40]], "inpaint_str_strand": null, "inpaint_str_loop": [["A", 25, 29], ["A", 65, 69]], "provide_seq": [[172, 177], [200, 205]], "length": "120-160"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This node only performs string parsing and validation; its runtime cost is negligible compared with the RF Diffusion computation itself.
- **Limitations**: All range strings must follow RF Diffusion contig syntax (optional chain ID plus 1‑based inclusive indices with start not greater than end). Any misformatted input will cause a validation error.
- **Behavior**: Empty strings are treated as an absence of constraint for that parameter and are converted to null or omitted in the resulting config, not passed through as empty strings.
- **Behavior**: The length field is interpreted purely as a length or length‑range constraint and is handled differently from the masking fields, which are parsed into explicit contig ranges.
- **Integration**: This node is intended as a configuration provider and should typically only be connected to the contigmap_config input of the RF Diffusion node.

## Troubleshooting
- **Invalid contig format**: If you see errors referring to contigs or ranges, one of the fields (such as inpaint_seq or inpaint_str) is malformed. Ensure each segment follows patterns like A10-25 or 30-40,50-60 and that the start index is less than or equal to the end index.
- **Masked regions not redesigning**: If regions you expect to change remain unchanged, verify that your ranges match the actual chain IDs and residue indices in the input PDB and that provide_seq is not simultaneously fixing those residues.
- **Issues in unconditional runs**: In unconditional designs (no input PDB), inconsistent use of length and masking ranges may cause downstream RF Diffusion failures. Start with a simple fixed length (for example, 100-100) and no inpainting, then incrementally add inpainting ranges.
- **No observable effect**: If all inpainting fields and length are empty, the node still outputs a config but it effectively imposes no additional contigmap constraints. Double‑check that the fields you expect to drive behavior are actually populated.
