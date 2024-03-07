# ModelMergeSDXLTransformers
## Documentation
- Class name: `ModelMergeSDXLTransformers`
- Category: `advanced/model_merging`
- Output node: `False`

This node is designed for merging two models by blending their components, specifically tailored for models with transformer blocks. It extends the functionality of a base model merging class to handle the intricacies of transformer-based models, allowing for detailed control over how different parts of the models are combined. This includes the ability to adjust the blending ratios of various components such as input, middle, and output blocks, as well as specific transformer elements within those blocks.
## Input types
- **`model1`**
    - The first model to be merged. It serves as the base model onto which elements from the second model are blended.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`model2`**
    - The second model whose elements are to be blended into the first model. This allows for the creation of a new model that inherits characteristics from both input models.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`time_embed.`**
    - Adjusts the blending ratio specifically for the time embedding components of the models.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`label_emb.`**
    - Sets the blending ratio for the label embedding components, allowing for customization of how label information is integrated between the two models.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`input_blocks.i.0.`**
    - Specifies the blending ratio for the initial part of the input blocks. The 'i' ranges from 0 to 8, enabling customization at the beginning of each input block.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`input_blocks.i.1.`**
    - Determines the blending ratio for the second part of the input blocks, applicable only to blocks with transformers. This allows for detailed control over the transformer sections within the input blocks.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`input_blocks.i.1.transformer_blocks.j.x`**
    - Controls the blending ratio for specific transformer block components within the input blocks, offering granular control over the merging of transformer elements. 'i' ranges from 0 to 8, 'j' corresponds to the transformer block index, and 'x' represents the specific transformer component.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`middle_block.i.`**
    - Sets the blending ratio for the middle blocks of the models, enabling precise adjustment of the core parts. 'i' ranges from 0 to 2, providing control over each middle block.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`middle_block.1.transformer_blocks.j.x`**
    - Adjusts the blending ratio for specific transformer block components within the middle block, allowing for detailed customization of the transformer elements in the core part of the model. 'j' corresponds to the transformer block index, and 'x' represents the specific transformer component.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`output_blocks.i.0.`**
    - Specifies the blending ratio for the initial part of the output blocks, enabling customization at the beginning of each output block. 'i' ranges from 0 to 8.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`output_blocks.i.1.`**
    - Determines the blending ratio for the second part of the output blocks, applicable only to blocks with transformers. This allows for detailed control over the transformer sections within the output blocks.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`output_blocks.i.1.transformer_blocks.j.x`**
    - Controls the blending ratio for specific transformer block components within the output blocks, offering granular control over the merging of transformer elements. 'i' ranges from 0 to 8, 'j' corresponds to the transformer block index, and 'x' represents the specific transformer component.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`out.`**
    - Sets the overall blending ratio for the models' output. This parameter provides a global control over the final output characteristics of the merged model.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The resulting model after merging the input models according to the specified blending ratios. This model combines elements from both input models, creating a new model with characteristics of both.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used for blending transformer-based models `MODEL1` and `MODEL2` to create a new model that inherits characteristics from both, allowing for detailed customization of blending ratios across various components including input, middle, and output blocks, as well as specific transformer elements within those blocks.
## Source code
```python
class ModelMergeSDXLTransformers(comfy_extras.nodes_model_merging.ModelMergeBlocks):
    @classmethod
    def INPUT_TYPES(s):
        arg_dict = { "model1": ("MODEL",),
                              "model2": ("MODEL",)}

        argument = ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01})

        arg_dict["time_embed."] = argument
        arg_dict["label_emb."] = argument

        transformers = {4: 2, 5:2, 7:10, 8:10}

        for i in range(9):
            arg_dict["input_blocks.{}.0.".format(i)] = argument
            if i in transformers:
                arg_dict["input_blocks.{}.1.".format(i)] = argument
                for j in range(transformers[i]):
                    arg_dict["input_blocks.{}.1.transformer_blocks.{}.".format(i, j)] = argument

        for i in range(3):
            arg_dict["middle_block.{}.".format(i)] = argument
            if i == 1:
                for j in range(10):
                    arg_dict["middle_block.{}.transformer_blocks.{}.".format(i, j)] = argument

        transformers = {3:2, 4: 2, 5:2, 6:10, 7:10, 8:10}
        for i in range(9):
            arg_dict["output_blocks.{}.0.".format(i)] = argument
            t = 8 - i
            if t in transformers:
                arg_dict["output_blocks.{}.1.".format(i)] = argument
                for j in range(transformers[t]):
                    arg_dict["output_blocks.{}.1.transformer_blocks.{}.".format(i, j)] = argument

        arg_dict["out."] = argument

        return {"required": arg_dict}

```
