# Rosetta Fast Relax

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs Rosetta FastRelax to refine protein structures. Given one or more sequences (FASTA) and corresponding initial structures (PDB), it performs a specified number of relaxation cycles to improve local geometry. Returns relaxed PDB structures, preserving input-to-output mapping by record IDs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/rosettafastrelaxnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after generating initial protein structures (e.g., from a folding model) to improve stereochemistry and relieve clashes. Provide the sequences and corresponding PDBs with matching IDs. Increase relax_cycles for more thorough refinement at the cost of runtime.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence(s) in FASTA format. If multiple sequences are provided, each record ID must match the corresponding structure ID in the PDB input.</td><td style="word-wrap: break-word;">>protein1 MKTAYIAKQRQISFVKSHFSRQ... >protein2 GHHHHHHSSGLVPRGSHMASMT...</td></tr>
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Initial structure(s) to relax. Provide as a dictionary mapping IDs to PDB text. IDs must correspond to the FASTA record IDs.</td><td style="word-wrap: break-word;">{"protein1": "ATOM      1  N   MET A   1 ...\nEND\n", "protein2": "ATOM      1  N   GLY A   1 ...\nEND\n"}</td></tr>
<tr><td style="word-wrap: break-word;">relax_cycles</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of relaxation cycles to apply to each structure. Higher values can yield better local geometry but increase runtime.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">debug</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, any pose error stops the run with an error. If false, the run continues and skips poses that fail.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Relaxed structures as a dictionary mapping input IDs to PDB content.</td><td style="word-wrap: break-word;">{"protein1": "ATOM ...\nEND\n", "protein2": "ATOM ...\nEND\n"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **ID matching required**: FASTA record IDs must correspond exactly to the structure IDs in the PDB input.
- **Not deterministic**: A seed parameter is not exposed; results may not be bitwise reproducible across runs.
- **Runtime scales with size**: Runtime increases with the number of sequences and relax_cycles.
- **Error handling**: With debug=true, any failure raises an error. With debug=false, failures for some poses are skipped; only successful relaxations are returned.
- **Purpose**: This node refines geometry; it does not perform folding from sequence.
- **Batch support**: Multiple sequences/structures can be processed in one run, provided IDs align.

## Troubleshooting
- **Missing outputs for some IDs**: If debug=false, failed poses are skipped. Re-run with debug=true to see the exact error, or reduce relax_cycles.
- **ID mismatch error**: Ensure FASTA headers (record IDs) match keys in the PDB dictionary exactly (including capitalization).
- **Long runtimes**: Lower relax_cycles or process fewer sequences per run. Check that input structures are reasonable (severely distorted inputs relax more slowly).
- **Invalid FASTA/PDB formats**: Validate that FASTA is properly formatted and each PDB entry contains standard ATOM/HETATM records with chain/residue numbering.
- **Downstream compatibility**: If downstream tools expect specific residue numbering, verify that relaxation preserved the mapping you rely on.
