# Alphafold Initial Guess

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs an AlphaFold-based initial refinement using provided sequences and corresponding starting structures. It produces predicted foldings and summary scores (RMSD, PAE, pLDDT) for each input, with controls for residue numbering, amide bond distance constraints, recycle count, and monomer enforcement.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Protein Structure Prediction/AlphafoldInitialGuessNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have one or more protein sequences with corresponding initial PDB structures and want AlphaFold to generate an initial guess/refined folding. Typical workflow: prepare FASTA and matching PDBs -> run Alphafold Initial Guess to get refined foldings and quality scores -> optionally post-process with relaxation or downstream analysis.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequences to process. Multiple sequences can be provided; record IDs must match the structure IDs in the PDB input.</td><td style="word-wrap: break-word;">>seq1 MSEQNNTEMTFQIQRIYTKDISFEAPNAPHVFQKDW >seq2 GHHHHHHMKTAYIAKQRQISFVKSHFSRQDILD</td></tr>
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Starting structures corresponding to the sequences. If multiple, their IDs must match the FASTA record IDs.</td><td style="word-wrap: break-word;">{   "seq1": "ATOM      1  N   MET A   1 ...\nEND\n",   "seq2": "ATOM      1  N   GLY A   1 ...\nEND\n" }</td></tr>
<tr><td style="word-wrap: break-word;">maintain_res_numbering</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prevents renumbering of residues even when inputs contain issues.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">max_amide_dist</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Maximum allowed distance (Å) between an amide bond's carbon and nitrogen.</td><td style="word-wrap: break-word;">3.0</td></tr>
<tr><td style="word-wrap: break-word;">recycle</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of AlphaFold recycles to perform.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">force_monomer</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, forces prediction in monomer mode.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">debug</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, errors will halt execution; if false, the process attempts to continue even if some poses fail.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Predicted foldings for each input, returned as a mapping of IDs to PDB content.</td><td style="word-wrap: break-word;">{   "seq1_rank_1": "ATOM ...\nEND\n",   "seq2_rank_1": "ATOM ...\nEND\n" }</td></tr>
<tr><td style="word-wrap: break-word;">score.sc</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Scores per design, including metrics such as RMSD, PAE, and pLDDT. Returned as a serialized string (e.g., JSON or tabular text) keyed by input IDs.</td><td style="word-wrap: break-word;">{"seq1": {"rmsd": 1.8, "pae": 7.2, "plddt": 85.6}, "seq2": {"rmsd": 2.3, "pae": 8.1, "plddt": 82.4}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **ID matching**: FASTA record IDs must exactly match the IDs of the input PDB structures; mismatches will cause errors.
- **Batch behavior**: Multiple sequences are supported; runtime scales with the number of inputs.
- **Residue numbering**: Enable maintain_res_numbering to preserve original numbering in challenging inputs.
- **Quality constraints**: max_amide_dist constrains amide geometry; tighten or relax as needed based on input quality.
- **Recycle trade-off**: Higher recycle values can improve accuracy but increase computation time.
- **Monomer mode**: Use force_monomer for single-chain predictions; disable when working with complex inputs that require multimeric context.

## Troubleshooting
- **Mismatched IDs between FASTA and PDB**: Ensure the FASTA headers and PDB mapping keys are identical (e.g., seq1 in both).
- **Geometry or amide bond errors**: Increase max_amide_dist or enable maintain_res_numbering to avoid renumbering and reduce failures.
- **Long runtimes or timeouts**: Reduce recycle, process fewer sequences at a time, or verify service availability.
- **Unexpected renumbering**: Set maintain_res_numbering to true to preserve original residue indices.
- **Low confidence (low pLDDT/high PAE)**: Provide higher-quality starting structures, adjust recycle upward, or check that the input sequence matches the structure.
