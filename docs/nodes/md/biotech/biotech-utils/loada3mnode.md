# Load A3M

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node ingests a raw A3M-formatted multiple sequence alignment string and converts it into Salt's MSA data type. You provide a logical identifier for the MSA, and the node outputs a dictionary mapping that ID to the original A3M content. It serves as a convenient entry point for integrating precomputed MSAs into biotech workflows such as AlphaFold-style structure prediction or protein design.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loada3mnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Load A3M node whenever you already have an A3M file or alignment text and need to bring it into a Salt workflow. Place it near the start of the pipeline, typically alongside nodes like LoadFastaNode and LoadPDBNode, to provide the MSA context required by downstream predictive or combinator nodes. For multi-chain or multi-domain workflows, instantiate multiple LoadA3MNode nodes with distinct `a3m_id` values (for example, `chainA_msa`, `chainB_msa`) and feed their outputs into A3MCombinerNode to produce a single combined MSA object. Ensure that the `a3m_id` is consistent with any related FASTA or PDB identifiers so that subsequent model nodes can correctly associate an MSA with its corresponding sequence or chain. This node is purely a loader and does not modify or validate the alignment; its primary role is to wrap user-provided A3M content in the correct MSA-typed structure for use by other biotech nodes.

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
<tr><td style="word-wrap: break-word;">a3m_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw A3M-formatted multiple sequence alignment content as a single text string. The string should include one or more sequences with header lines starting with '>' followed by aligned amino-acid sequences, following standard A3M conventions (for example, uppercase core positions and lowercase insertions). Newlines and formatting must be preserved, as downstream tools rely on the exact alignment layout. Empty or non-A3M strings will still pass through this node but are likely to cause errors or poor results in later MSA-consuming nodes.</td><td style="word-wrap: break-word;">>query_chainA MKTLLILAVAAAGLAVSDQASA---GKTK >homolog1 MKTLVILAVAAAGLAVSDQASA---GKTK >homolog2 MKTLLILAVAAAGLAVSDQASASSSGKTK </td></tr>
<tr><td style="word-wrap: break-word;">a3m_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Logical identifier that will be used as the key for this MSA in the output dictionary. It must be a non-empty string and should be unique across different MSAs within the same workflow to avoid key conflicts. In complex pipelines, choose IDs that clearly map to corresponding sequences or chains (for example, match PDB chain IDs or FASTA headers) so downstream nodes can reliably match MSAs to their targets.</td><td style="word-wrap: break-word;">chainA_msa</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">An MSA-typed dictionary that maps the specified `a3m_id` to the provided A3M alignment string. Conceptually, the structure is `{ a3m_id: a3m_string }`. This object is compatible with MSA-aware biotech nodes such as A3MCombinerNode (to merge multiple MSA dictionaries into one) and any AlphaFold-like or analysis nodes that accept MSA inputs. The A3M text itself is unchanged; only its packaging and type metadata are added.</td><td style="word-wrap: break-word;">{'chainA_msa': '>query_chainA\nMKTLLILAVAAAGLAVSDQASA---GKTK\n>homolog1\nMKTLVILAVAAAGLAVSDQASA---GKTK\n>homolog2\nMKTLLILAVAAAGLAVSDQASASSSGKTK\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a simple wrap of the input string into a dictionary and attaches metadata, so it is effectively instantaneous and does not materially affect pipeline performance.
- **Limitations**: No syntactic or semantic validation of A3M content is performed. Misformatted alignments, inconsistent line lengths, or invalid characters will only surface as issues in downstream model or analysis nodes.
- **Behavior**: The dictionary key in the output is exactly the user-supplied `a3m_id`. Reusing the same `a3m_id` for multiple MSAs in later combiner nodes can cause key collisions or forced renaming, depending on how those nodes handle conflicts.
- **Workflow design**: In multi-chain or multi-target settings, define a consistent ID scheme across LoadA3MNode, LoadFastaNode, and structure-loading nodes (for example, `chainA`, `chainB`) so that all components—sequence, structure, and MSA—stay correctly associated in downstream steps.

## Troubleshooting
- **Common Error 1**: A downstream model or analysis node reports that no MSA is available for a particular chain or sequence. Check that `a3m_id` exactly matches the chain or sequence identifier expected by that node and that the Load A3M output is correctly wired into its MSA input.
- **Common Error 2**: Later nodes emit messages like 'failed to parse A3M' or 'invalid alignment format'. Open the original A3M string and confirm that headers start with '>', sequences are aligned with consistent line widths, and there are no stray characters or encoding issues. Regenerate or clean the MSA if necessary.
- **Common Error 3**: When using A3MCombinerNode, some MSAs appear to be missing or unexpectedly renamed. Ensure each LoadA3MNode uses a distinct `a3m_id`, and, if you still see collisions, use the `prefix_chains` option in A3MCombinerNode to automatically prefix chain IDs and avoid conflicts.
