# ModelMergeSDXLDetailedTransformers
## Documentation
- Class name: `ModelMergeSDXLDetailedTransformers`
- Category: `advanced/model_merging`
- Output node: `False`

This node specializes in merging models with a detailed focus on transformer blocks. It inherits from a base model merging class and is designed to handle complex merging scenarios involving transformer components within models. The node likely incorporates advanced techniques to merge specific parts of models, such as transformer blocks, with precision, allowing for fine-tuned control over the merging process.
## Input types
- **`model1`**
    - The first model to be merged. It is a crucial component of the merging process, serving as one of the primary sources of features or parameters to be combined.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`model2`**
    - The second model to be merged. It plays a significant role in the merging process, contributing its features or parameters to be combined with those of the first model.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`time_embed.`**
    - A floating-point parameter influencing the merging process, likely related to the timing embedding aspects of the models.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`label_emb.`**
    - A floating-point parameter affecting the merging process, possibly related to the label embedding aspects of the models.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`input_blocks.i`**
    - A series of floating-point parameters for each input block, allowing for fine-tuned control over how these blocks are merged. The index 'i' ranges from 0 to 8, with additional parameters for transformer blocks within certain input blocks.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`middle_block.i`**
    - A series of floating-point parameters for each middle block, enabling precise adjustments in the merging of these blocks. The index 'i' ranges from 0 to 2, with additional parameters for transformer blocks within the middle block at index 1.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`output_blocks.i`**
    - A series of floating-point parameters for each output block, facilitating detailed control over the merging of these blocks. The index 'i' ranges from 0 to 8, with additional parameters for transformer blocks within certain output blocks, based on a reverse index mapping.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`out.`**
    - A floating-point parameter that likely influences the final output aspects of the merging process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The result of the merging process, a single model that combines elements from both input models, with particular attention to transformer blocks.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The ModelMergeSDXLDetailedTransformers node is designed for advanced scenarios where two AI models, particularly those with transformer blocks, need to be merged with high precision. It takes two models as input, along with various parameters to fine-tune the merging of transformer components, and outputs a single, integrated model that combines elements from both input models, focusing on transformer blocks. This node is often used in pipelines requiring the nuanced integration of transformer-based models for tasks like language processing or generative tasks.
## Source code
```python
class ModelMergeSDXLDetailedTransformers(comfy_extras.nodes_model_merging.ModelMergeBlocks):
    @classmethod
    def INPUT_TYPES(s):
        arg_dict = { "model1": ("MODEL",),
                              "model2": ("MODEL",)}

        argument = ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01})

        arg_dict["time_embed."] = argument
        arg_dict["label_emb."] = argument

        transformers = {4: 2, 5:2, 7:10, 8:10}
        transformers_args = ["norm1", "attn1.to_q", "attn1.to_k", "attn1.to_v", "attn1.to_out", "ff.net", "norm2", "attn2.to_q", "attn2.to_k", "attn2.to_v", "attn2.to_out", "norm3"]

        for i in range(9):
            arg_dict["input_blocks.{}.0.".format(i)] = argument
            if i in transformers:
                arg_dict["input_blocks.{}.1.".format(i)] = argument
                for j in range(transformers[i]):
                    for x in transformers_args:
                        arg_dict["input_blocks.{}.1.transformer_blocks.{}.{}".format(i, j, x)] = argument

        for i in range(3):
            arg_dict["middle_block.{}.".format(i)] = argument
            if i == 1:
                for j in range(10):
                    for x in transformers_args:
                        arg_dict["middle_block.{}.transformer_blocks.{}.{}".format(i, j, x)] = argument

        transformers = {3:2, 4: 2, 5:2, 6:10, 7:10, 8:10}
        for i in range(9):
            arg_dict["output_blocks.{}.0.".format(i)] = argument
            t = 8 - i
            if t in transformers:
                arg_dict["output_blocks.{}.1.".format(i)] = argument
                for j in range(transformers[t]):
                    for x in transformers_args:
                        arg_dict["output_blocks.{}.1.transformer_blocks.{}.{}".format(i, j, x)] = argument

        arg_dict["out."] = argument

        return {"required": arg_dict}

```
