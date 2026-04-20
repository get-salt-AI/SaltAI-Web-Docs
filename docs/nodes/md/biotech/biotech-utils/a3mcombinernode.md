# A3M Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node merges multiple A3M MSA inputs, each represented as a dictionary mapping chain or sequence IDs to A3M-formatted strings, into one consolidated A3M dictionary. It can optionally prefix chain IDs per input source to avoid ID clashes and will further resolve any remaining conflicts by appending numeric suffixes. The resulting merged MSA is suitable for downstream structure prediction or analysis nodes that expect a single A3M input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/a3mcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have several A3M multiple sequence alignments (for example, from different databases, search parameters, or chain-specific runs) and need to provide them as a single combined MSA object. Place it after nodes like LoadA3MNode (which outputs MSA dictionaries of the form {a3m_id: a3m_string}) or other biotech nodes that produce MSA-typed dictionaries, connecting up to five of these into inputs a3m_1–a3m_5. In multi-chain protein workflows, combine per-chain MSAs with this node (typically with prefix_chains enabled) so that downstream structure prediction or design nodes receive a unified but collision-free mapping of chain IDs to MSAs. A common pattern is: load PDBs with LoadPDBNode, load or generate chain MSAs with multiple LoadA3MNode instances, optionally align naming conventions, then merge with A3MCombinerNode and feed the result into AlphaFold-style or other biotech model nodes that consume a single MSA input together with PDB/FASTA inputs (possibly combined via PDBCombinerNode or FastaCombinerNode). Prefer enabling prefix_chains when MSAs originate from different runs or represent distinct logical groups, to keep provenance clear and to minimize ambiguous chain ID reuse.

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
<tr><td style="word-wrap: break-word;">a3m_1</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">First A3M MSA dictionary. Must be a mapping from chain or sequence IDs (keys) to A3M-formatted MSA text (values). If present, its entries are merged first into the combined dictionary.</td><td style="word-wrap: break-word;">{'A': '>A/1-120\nMKTLLILAVAVAFASASAA...\n>A_HOMOLOG1\nMKTLFVLAVAVAFASAASA...\n', 'B': '>B/1-80\nGSSHHHHHHSSGLVPRGSHM...\n'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_2</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Second A3M MSA dictionary to be merged. Same structure as a3m_1. If chain IDs overlap with other inputs, they may be prefixed or renumbered depending on prefix_chains.</td><td style="word-wrap: break-word;">{'A': '>A/1-120\nMKTLLILAVAVAFASASAT...\n>A_HOMOLOG2\nMKTLFVLAVAVAFASAASV...\n'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_3</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Third optional A3M MSA dictionary. Useful for adding additional MSAs from other search runs, databases, or chain variants.</td><td style="word-wrap: break-word;">{'C': '>C/5-135\nTTGQNVQLAVDDFRTKVL...\n'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_4</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Fourth optional A3M MSA dictionary. Like the other A3M inputs, must be a dict of chain IDs to A3M-formatted strings.</td><td style="word-wrap: break-word;">{'A': '>A/1-120\nMKTLLILAVAVAFASASAA...\n>A_UNIPROT_P12345\nMKTLLILAVAVAFASASAA...\n'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_5</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Fifth optional A3M MSA dictionary. Any subset of a3m_1–a3m_5 may be left unconnected; at least one valid MSA dictionary is required overall.</td><td style="word-wrap: break-word;">{'D': '>D/10-150\nADKLSNFVKQLAAEAGLD...\n'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, each input dictionary’s chain IDs are prefixed with its input index (A3M1_, A3M2_, and so on), reducing the chance of ID collisions across different inputs. If false, original chain IDs are used; when duplicates occur, the node logs a warning and appends numeric suffixes (for example, A, A_1, A_2) so that all entries remain accessible.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Single merged A3M MSA dictionary combining all non-empty A3M inputs. Keys are final chain or sequence IDs (possibly prefixed and/or suffixed), and values are their A3M-formatted MSA strings. This object is intended to be wired directly into downstream biotech nodes that expect one MSA dictionary as input.</td><td style="word-wrap: break-word;">{'A3M1_A': '>A/1-120\nMKTLLILAVAVAFASASAA...\n>A_HOMOLOG1\nMKTLFVLAVAVAFASAASA...\n', 'A3M2_A': '>A/1-120\nMKTLLILAVAVAFASASAT...\n>A_HOMOLOG2\nMKTLFVLAVAVAFASAASV...\n', 'C': '>C/5-135\nTTGQNVQLAVDDFRTKVL...\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs in-memory dictionary and string operations only; it is typically very fast, but very large A3M files (many thousands of sequences) may increase memory use and processing time.
- **Limitations**: It requires at least one non-empty A3M dictionary among a3m_1–a3m_5; otherwise it raises a ValueError indicating that at least one A3M input is required.
- **Behavior**: When prefix_chains is false, identical chain IDs across different inputs are allowed but will trigger a logged warning; the node automatically appends numeric suffixes to create unique keys (for example, A, then A_1, A_2).
- **Behavior**: When prefix_chains is true, each input’s IDs are prefixed (A3M1_, A3M2_, and so on). If conflicts still occur under these new names, additional numeric suffixes are appended to keep every entry unique and prevent overwriting.

## Troubleshooting
- **Common Error 1**: Error message similar to `At least one A3M input is required` – All A3M inputs are either unconnected, empty, or not proper dicts. Fix by connecting at least one valid MSA output (for example, from LoadA3MNode) to a3m_1 or another A3M input.
- **Common Error 2**: Downstream node reports missing or mismatched chain IDs – The IDs in combined_a3m may differ from original ones due to prefixing or conflict resolution. Inspect the keys of combined_a3m and update downstream configuration or ensure your PDB or FASTA inputs use matching chain identifiers.
- **Common Issue 3**: Unexpected suffixes on chain IDs (for example, A3M1_A_2) – This indicates that the same chain ID appeared more than once in the combined set, so the node generated unique variants with numeric suffixes. To keep IDs more predictable, enable prefix_chains and maintain distinct chain IDs within each input where possible.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/a3mcombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

