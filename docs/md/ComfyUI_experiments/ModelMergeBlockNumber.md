# ModelMergeBlockNumber
## Documentation
- Class name: `ModelMergeBlockNumber`
- Category: `advanced/model_merging`
- Output node: `False`

This node specializes in merging two models by applying a weighted combination of their components. It allows for fine-grained control over the merging process by specifying weights for different parts of the models, such as time embeddings, label embeddings, input blocks, middle blocks, output blocks, and the output layer. This enables the creation of hybrid models that blend features from both input models according to specified ratios.
## Input types
- **`model1`**
    - The first model to be merged. It serves as the base model for the merging process.
    - Python dtype: `comfy.model_management.Model`
    - Comfy dtype: `MODEL`
- **`model2`**
    - The second model to be merged. Its components are selectively blended into the first model based on the specified weights.
    - Python dtype: `comfy.model_management.Model`
    - Comfy dtype: `MODEL`
- **`time_embed.`**
    - Specifies the weight for merging the time embedding components of the two models. Allows for precise control over how time-related features are combined.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`label_emb.`**
    - Determines the weight for merging the label embedding components. This parameter enables customization of how label-related features are integrated.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`input_blocks.i`**
    - Controls the weight for merging the i-th input block, where i ranges from 0 to 11. This allows for detailed customization of the initial stages of the model.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`middle_block.i`**
    - Sets the weight for merging the i-th middle block, where i ranges from 0 to 2. This parameter influences the blending of the models' intermediate processing stages.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`output_blocks.i`**
    - Specifies the weight for merging the i-th output block, where i ranges from 0 to 11. It affects how the final stages of the models are combined.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`out.`**
    - Determines the weight for merging the output layer components. This parameter finalizes the blending of the models' output features.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The resulting model after merging the specified components of the two input models.
    - Python dtype: `comfy.model_management.Model`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The ModelMergeBlockNumber node is primarily utilized for creating hybrid AI models by merging two distinct models, allowing for the customization of the merging process through weighted parameters for different model components, including time and label embeddings, input, middle, and output blocks, and the output layer. This node takes two models as input and outputs a single model that combines features from both according to specified weights, making it ideal for experiments in model blending and performance enhancement.
## Source code
```python
class ModelMergeBlockNumber(comfy_extras.nodes_model_merging.ModelMergeBlocks):
    @classmethod
    def INPUT_TYPES(s):
        arg_dict = { "model1": ("MODEL",),
                              "model2": ("MODEL",)}

        argument = ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01})

        arg_dict["time_embed."] = argument
        arg_dict["label_emb."] = argument

        for i in range(12):
            arg_dict["input_blocks.{}.".format(i)] = argument

        for i in range(3):
            arg_dict["middle_block.{}.".format(i)] = argument

        for i in range(12):
            arg_dict["output_blocks.{}.".format(i)] = argument

        arg_dict["out."] = argument

        return {"required": arg_dict}

```
