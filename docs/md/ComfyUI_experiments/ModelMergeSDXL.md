# ModelMergeSDXL
## Documentation
- Class name: `ModelMergeSDXL`
- Category: `advanced/model_merging`
- Output node: `False`

This node merges two models by blending their components based on specified ratios for different parts of the models. It allows for fine-tuned control over how much of each model's characteristics are retained in the merged model, by adjusting ratios for time embeddings, label embeddings, input blocks, middle blocks, output blocks, and the output layer.
## Input types
- **`model1`**
    - The first model to be merged. It serves as the base model for the merging process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`model2`**
    - The second model to be merged. Its components are blended with the first model based on the specified ratios.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`time_embed.`**
    - Specifies the blending ratio for the time embedding components of the models.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`label_emb.`**
    - Specifies the blending ratio for the label embedding components of the models.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`input_blocks.i`**
    - Specifies the blending ratio for the i-th input block of the models, where i ranges from 0 to 8.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`middle_block.i`**
    - Specifies the blending ratio for the i-th middle block of the models, where i ranges from 0 to 2.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`output_blocks.i`**
    - Specifies the blending ratio for the i-th output block of the models, where i ranges from 0 to 8.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`out.`**
    - Specifies the blending ratio for the output layer of the models.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The resulting model after merging the two input models based on the specified blending ratios.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The ModelMergeSDXL node is primarily utilized for blending two AI models by adjusting the ratios for specific components like time embeddings, label embeddings, input, middle, and output blocks, and the output layer, making it ideal for creating hybrid models that combine the strengths of both input models. It requires two models as input and outputs a single model that embodies the merged characteristics based on the specified blending ratios.
## Source code
```python
class ModelMergeSDXL(comfy_extras.nodes_model_merging.ModelMergeBlocks):
    @classmethod
    def INPUT_TYPES(s):
        arg_dict = { "model1": ("MODEL",),
                              "model2": ("MODEL",)}

        argument = ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01})

        arg_dict["time_embed."] = argument
        arg_dict["label_emb."] = argument

        for i in range(9):
            arg_dict["input_blocks.{}".format(i)] = argument

        for i in range(3):
            arg_dict["middle_block.{}".format(i)] = argument

        for i in range(9):
            arg_dict["output_blocks.{}".format(i)] = argument

        arg_dict["out."] = argument

        return {"required": arg_dict}

```
