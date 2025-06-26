# ProteinMPNN

Predicts possible amino acid sequences for a given protein backbone, returning generated and top-scoring sequences.

<img src="/images/nodes/biotech/sequence-prediction/protein_mpnn.png" alt="ProteinMPNN" class="rounded-lg">

## Quick Start

1. Prepare your input PDB structure(s).
2. Set the desired model parameters (e.g., model version, number of sequences).
3. Connect the node and run to generate protein sequences.

## Setup Guide

### 1. Prepare Input Data
1. Ensure your PDB input is a dictionary `{pdb_id: pdb_content}`.
2. Optionally, specify chains to design or amino acids to omit.

### 2. Configure Node Parameters
1. Select model version and adjust generation settings (e.g., batch size, temperature).
2. Set reproducibility seed if needed.

## Basic Usage

### Sequence Design for Protein Backbones
* Generate diverse amino acid sequences for a given protein structure.
* Restrict design to specific chains or omit certain amino acids.
* Use batch mode for multiple structures.

## Configuration

### Required Inputs
| Field              | Description                                                        | Type    | Example         |
|--------------------|--------------------------------------------------------------------|---------|-----------------|
| pdb                | Structures to run through the model.                              | PDB     | {"A": "..."}   |
| ca_only            | Whether to parse CA-only structures and use CA-only models.        | BOOLEAN | False           |
| model_name         | ProteinMPNN model name.                                            | COMBO   | v_48_020        |
| use_soluble_model  | Whether to load weights trained on soluble proteins only.          | BOOLEAN | False           |
| backbone_noise     | Std. dev. of Gaussian noise to add to backbone atoms.              | FLOAT   | 0.0             |
| num_seq_per_target | Number of sequences to generate per protein.                      | INT     | 10              |
| num_best_seq_per_target | Number of top-scoring sequences to return.                    | INT     | 1               |
| batch_size         | Batch size. Reduce if running out of GPU memory.                  | INT     | 1               |
| max_length         | Max sequence length.                                               | INT     | 200000          |
| sampling_temp      | Temperatures for amino acids (comma separated).                    | STRING  | 0.1,0.2,0.3     |
| omit_amino_acids   | Amino acids to omit (comma separated).                             | STRING  | X               |
| chains_to_design   | Chains to design (comma separated, empty for all).                | STRING  | A,B             |
| seed               | Base seed value for randomness.                                   | INT     | 42              |
| mode               | Mode to run the node in (MOCK, PROD, TEST).                       | COMBO   | PROD            |

### Optional Inputs
*None*

### Outputs
| Field            | Description                    | Example         |
|------------------|--------------------------------|-----------------|
| seqs.fasta       | All generated sequences.       | >A_seq_0\n...   |
| best_seqs.fasta  | Top best generated sequences.  | >A_best_seq_0\n... |

## Best Practices

### Sequence Generation
* Use higher sampling temperatures for more diverse sequences.
* Adjust batch size based on available GPU memory.

### Model Selection
* Choose the model version and soluble weights according to your protein type.
* For CA-only structures, ensure the model supports CA-only mode.

## Troubleshooting

### Common Issues
* **Error: CA-SolubleMPNN model is not available yet.**: Set either `ca_only` or `use_soluble_model` to False.
* **Error: CA-v_48_030 model is not available yet.**: Use a different model or set `ca_only` to False.
* **num_best_seq_per_target > num_seq_per_target**: Ensure the number of best sequences does not exceed total generated.

### Need Help?
* [ProteinMPNN GitHub](https://github.com/dauparas/ProteinMPNN)
* [SaltAI Documentation](https://docs.salt.ai)
