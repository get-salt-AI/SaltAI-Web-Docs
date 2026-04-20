# PDB Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

PDBCombinerNode merges multiple PDB inputs, each as a dictionary mapping identifiers to PDB text content, into a single unified PDB dictionary. It can optionally prefix identifiers from each input (e.g., PDB1_, PDB2_) and always ensures unique keys by adding numeric suffixes when collisions occur. This lets you assemble multi-structure or multi-chain collections from several upstream steps into one standardized PDB input for downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use PDBCombinerNode when you have several PDB dictionaries to treat as one combined set of structures, such as merging separate design libraries, combining results from multiple runs, or aggregating docking poses. Place it after nodes that output PDB dictionaries, like LoadPDBNode, BatchPDBNode, PdbFixerNode, or PDBChainExtractorNode, and before downstream nodes like PDBToFastaNode, PDBChainExtractorNode, PdbFixerNode, or PDBVisualizationNode that consume a single PDB mapping. Connect any subset of pdb_1–pdb_5 to these PDB outputs. If you expect overlapping identifiers (e.g., chain IDs or structure IDs reused across inputs), enable prefix_chains so the node automatically rewrites them as PDB1_<id>, PDB2_<id>, etc. With prefix_chains turned off, the node still preserves all entries by appending numeric suffixes on duplicates and logs warnings to highlight conflicts. Prefer enabling prefix_chains when merging unrelated sets or repeated runs, and keep it off if you already enforce globally unique IDs earlier in your workflow.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First optional PDB dictionary to include in the combination. Must be a mapping of identifiers (for example, structure or chain IDs) to PDB text content. If not connected or empty, it is ignored.</td><td style="word-wrap: break-word;">{'1ABC_chain_A': 'HEADER    EXAMPLE PDB\nATOM      1  N   MET A   1      11.104  13.207   9.329  1.00 20.00           N\nATOM      2  CA  MET A   1      12.560  13.400   9.600  1.00 20.00           C\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_2</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Second optional PDB dictionary to merge. Same structure as pdb_1. Any overlapping identifiers will be handled based on the prefix_chains setting and conflict resolution logic.</td><td style="word-wrap: break-word;">{'2XYZ_chain_A': 'HEADER    ANOTHER PDB\nATOM      1  N   GLY A   1       5.104   8.207   1.329  1.00 18.00           N\nEND', '2XYZ_chain_B': 'HEADER    ANOTHER PDB\nATOM      1  N   ALA B   1       6.234   9.112   2.450  1.00 18.00           N\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_3</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Third optional PDB dictionary. Useful when combining three separate PDB collections into a single merged mapping.</td><td style="word-wrap: break-word;">{'design1': 'HEADER    DESIGN MODEL 1\nATOM      1  N   SER A   1      10.000  10.000  10.000  1.00 15.00           N\nEND', 'design2': 'HEADER    DESIGN MODEL 2\nATOM      1  N   SER A   1      11.000  11.000  11.000  1.00 15.00           N\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_4</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourth optional PDB dictionary. Behaves the same as pdb_1–pdb_3; if not connected, it is simply ignored.</td><td style="word-wrap: break-word;">{'scaffold_A': 'HEADER    SCAFFOLD A\nATOM      1  N   THR A   1      20.000  20.000  20.000  1.00 12.00           N\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_5</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifth optional PDB dictionary. Allows merging up to five separate PDB sources in a single step.</td><td style="word-wrap: break-word;">{'binder_pose_1': 'HEADER    BINDER POSE 1\nATOM      1  N   LYS A   1      30.000  30.000  30.000  1.00 10.00           N\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes each identifier from pdb_i with PDB<i>_ (for example, PDB1_1ABC_chain_A) to avoid ID collisions across inputs. When false, identifiers are used as-is; if duplicates occur, the node logs a warning and appends numeric suffixes to make them unique.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Single merged PDB dictionary containing entries from all non-empty pdb_1–pdb_5 inputs. Keys are final, unique identifiers (possibly prefixed or suffixed), and values are the original PDB text strings. This output can be consumed by any node that expects a PDB mapping, such as PDBToFastaNode, PDBChainExtractorNode, PdbFixerNode, or PDBVisualizationNode.</td><td style="word-wrap: break-word;">{'PDB1_1ABC_chain_A': 'HEADER    EXAMPLE PDB\nATOM      1  N   MET A   1      11.104  13.207   9.329  1.00 20.00           N\nEND', 'PDB2_2XYZ_chain_A': 'HEADER    ANOTHER PDB\nATOM      1  N   GLY A   1       5.104   8.207   1.329  1.00 18.00           N\nEND', 'PDB2_2XYZ_chain_B': 'HEADER    ANOTHER PDB\nATOM      1  N   ALA B   1       6.234   9.112   2.450  1.00 18.00           N\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node iterates over all keys and values in up to five input dictionaries; for very large PDB collections, execution time and memory usage scale with the total number of entries, but it makes no external API calls.
- **Limitations**: At least one of pdb_1–pdb_5 must be a non-empty dictionary; if all are missing or empty, the node raises a ValueError with the message "At least one PDB input is required".
- **Behavior**: When prefix_chains is false and the same identifier appears in multiple inputs, the node does not overwrite existing data; it logs a warning and automatically renames later duplicates using numeric suffixes to keep all entries.
- **Behavior**: When prefix_chains is true, each input dictionary has its identifiers prefixed by its slot index (PDB1_, PDB2_, and so on); if even these prefixed identifiers collide, additional numeric suffixes are appended to ensure uniqueness.
- **Behavior**: Inputs that are not provided, are null, or are not dictionaries are skipped, so you can safely connect only the subset of pdb_i ports you need without extra configuration.
- **Integration**: The node preserves the raw PDB text content exactly as received; only the dictionary keys (identifiers) may be modified by prefixing or conflict resolution.

## Troubleshooting
- **All inputs skipped or error raised**: If you see the error "At least one PDB input is required", verify that at least one of pdb_1–pdb_5 is connected and that its upstream node outputs a non-empty PDB dictionary instead of an empty or incompatible value.
- **Unexpected renamed identifiers**: If expected IDs (for example, 1ABC_chain_A) appear as 1ABC_chain_A_1 or similar in the output, there were duplicate IDs across inputs. Enable prefix_chains to separate sources (PDB1_, PDB2_, and so on) or adjust upstream IDs to be unique.
- **Missing structures in output**: If some intended structures do not appear in combined_pdb, check that the corresponding pdb_i input is wired and that the upstream node emitted a proper PDB dictionary, not an empty dict or a different type.
- **Type-related issues**: If an input seems ignored, confirm that the wire going into that pdb_i port is a PDB-typed dictionary from nodes like LoadPDBNode, BatchPDBNode, PDBChainExtractorNode, or PdbFixerNode, rather than a plain string or a FASTA/MSA type.
