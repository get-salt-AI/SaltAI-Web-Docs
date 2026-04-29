# A3M Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

A3MCombinerNode merges multiple A3M MSA inputs, each as a dictionary mapping chain IDs to A3M-formatted strings, into a single combined A3M dictionary. It can optionally prefix chain IDs per input index to avoid collisions and auto-renames duplicates with numeric suffixes when needed. The node requires at least one non-empty A3M input and leaves the MSA string contents unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/a3mcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use A3MCombinerNode when you have several A3M multiple-sequence alignments that must be provided as a single MSA object to downstream structure prediction or analysis nodes. Typical upstream nodes include LoadA3MNode and custom MSA generators that output the MSA type as a dict of {chain_id: a3m_string}. Place this node after all MSA preparation steps and before AlphaFold-style or other biotech nodes that accept one combined MSA input. Connect between one and five inputs (a3m_1–a3m_5); any that are unconnected or empty are ignored. Enable prefix_chains when different inputs reuse the same chain IDs (for example, multiple complexes or runs with chain "A") so that output keys become A3M1_A, A3M2_A, etc., ensuring uniqueness and traceability. For coordinated pipelines, align its chain ID conventions with PDBCombinerNode and FastaCombinerNode so that PDB, FASTA, and A3M refer to chains consistently.

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
<tr><td style="word-wrap: break-word;">a3m_1</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">First A3M MSA dictionary to include. Must be a dictionary mapping chain IDs (such as "A", "B", or "A_chain1") to A3M-formatted MSA strings. Ignored if missing, not a dict, or empty.</td><td style="word-wrap: break-word;">{ "A": ">query/1-120\nMKTAYIAKQRQISFVKSHFSRQ\n>homolog1\nMKTAYIAKQREISFVKSHFSRQ\n" }</td></tr>
<tr><td style="word-wrap: break-word;">a3m_2</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Second A3M MSA dictionary, merged into the combined output if it is a non-empty dictionary.</td><td style="word-wrap: break-word;">{ "A": ">query_alt/1-120\nMKTAYIAKQRQISFVKSHFSRQ\n>env_hit1\nMKTAYIAKQRQISFVKSHFTRQ\n" }</td></tr>
<tr><td style="word-wrap: break-word;">a3m_3</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Third optional A3M MSA dictionary, for example an MSA for another chain or from a different search pipeline.</td><td style="word-wrap: break-word;">{ "B": ">chainB_query/1-80\nGASSSGSSGSSGGSSGSSGG\n" }</td></tr>
<tr><td style="word-wrap: break-word;">a3m_4</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Fourth optional A3M MSA dictionary. If not connected or if not a non-empty dictionary, it has no effect.</td><td style="word-wrap: break-word;">{ "A": ">deepmsa_query/1-120\nMKTAYIAKQRQISFVKSHFSRQ\n" }</td></tr>
<tr><td style="word-wrap: break-word;">a3m_5</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Fifth optional A3M MSA dictionary, allowing aggregation of up to five independent MSA sources into a single combined mapping.</td><td style="word-wrap: break-word;">{ "C": ">chainC_query/1-50\nMSSHHHHHHSSGLVPRGSH\n" }</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, chain IDs from each input are prefixed with "A3M{i}_" based on the input index (e.g., A3M1_A, A3M2_B). If false, original chain IDs are used and collisions are resolved by appending numeric suffixes (_1, _2, ...) instead of overwriting. Default behavior is false.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Single merged A3M MSA dictionary constructed from all non-empty inputs. Keys are unique chain IDs (optionally prefixed and/or suffixed) and values are the original A3M-formatted strings. This is the MSA object to feed into downstream prediction or analysis nodes that expect one combined MSA input.</td><td style="word-wrap: break-word;">{ "A3M1_A": ">query/1-120\nMKTAYIAKQRQISFVKSHFSRQ\n>homolog1\nMKTAYIAKQREISFVKSHFSRQ\n", "A3M2_A": ">query_alt/1-120\nMKTAYIAKQRQISFVKSHFSRQ\n>env_hit1\nMKTAYIAKQRQISFVKSHFTRQ\n", "A3M3_B": ">chainB_query/1-80\nGASSSGSSGSSGGSSGSSGG\n" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node only iterates over up to five dictionaries and copies keys/values, so it is lightweight; only extremely large MSAs will noticeably impact time or memory.
- **Limitations**: At least one of a3m_1–a3m_5 must be a non-empty dictionary; otherwise the node raises a ValueError indicating that at least one A3M input is required.
- **Behavior**: With `prefix_chains` set to true, chain IDs are prefixed by input index (A3M1_, A3M2_, etc.) and, if needed, numeric suffixes (_1, _2, ...) are added to keep keys unique.
- **Behavior**: With `prefix_chains` set to false, repeated chain IDs across inputs trigger a warning and are disambiguated with numeric suffixes instead of overwriting earlier entries; MSA string contents are never altered.
- **Behavior**: The node treats inputs as plain dictionaries and does not validate that value strings are syntactically correct A3M, so upstream processing must ensure valid A3M formatting.

## Troubleshooting
- **Common Error 1**: Error stating that at least one A3M input is required. This occurs when all a3m_1–a3m_5 are unconnected, None, not dicts, or empty. Connect at least one valid MSA dictionary output or verify that upstream nodes are producing non-empty MSA dictionaries.
- **Common Error 2**: Downstream nodes see chain IDs like `A3M1_A` instead of `A`. This is caused by `prefix_chains` being true. Disable `prefix_chains` or adjust your PDB/FASTA nodes and downstream configuration to expect the prefixed IDs.
- **Common Error 3**: PDB or FASTA inputs use chain IDs `A`, `B`, but the combined MSA uses `A_1`, `A_2` due to conflict resolution, leading to mismatch in downstream tools that require matching IDs. Ensure chain IDs are unique across MSAs or harmonize naming by applying a similar prefixing/renaming pattern with PDBCombinerNode and FastaCombinerNode.
- **Common Error 4**: Connecting a non-dictionary object (for example, a plain string) to an a3m_* input results in that input being ignored. Update the upstream node to output a dictionary of {chain_id: a3m_string} so it is included in the combined result.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/a3mcombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

