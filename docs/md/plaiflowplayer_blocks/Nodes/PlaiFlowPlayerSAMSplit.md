# Split SAM Data (PlaiFlow Player)
## Documentation
- Class name: `PlaiFlowPlayerSAMSplit`
- Category: `PlaiFlow Player`
- Output node: `False`

The `PlaiFlowPlayerSAMSplit` node is designed to process and split SAM (Spatial Analysis Map) data into its constituent parameters and an image. It validates the input to ensure it is in the expected tuple format, suitable for further analysis or visualization.
## Input types
### Required
- **`input_node_sam_value`**
    - This parameter represents the SAM data to be processed. It is crucial for the operation as it contains the data that will be split into parameters and an image for further use.
    - Python dtype: `Tuple[Dict[str, np.ndarray], Optional[Image.Image]]`
    - Comfy dtype: `*`
## Output types
- **`*`**
    - Contains the parameters extracted from the SAM data, essential for understanding or analyzing the spatial aspects.
    - Python dtype: `Dict[str, np.ndarray]`
    - Comfy dtype: `*`
- **`image`**
    - Represents the image part of the SAM data, useful for visualization purposes.
    - Python dtype: `Optional[Image.Image]`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The `PlaiFlowPlayerSAMSplit` node is essential for processing and splitting SAM (Spatial Analysis Map) data into parameters and an image, making it crucial for tasks that require detailed spatial analysis or visualization. It is often used with nodes designed for visualizing spatial data or further analyzing the extracted parameters.
## Source code
```python
class PlaiFlowPlayerSAMSplit:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "input_node_sam_value": (WILDCARD,)
            },
        }

    RETURN_TYPES = (WILDCARD, "IMAGE")
    RETURN_NAMES = ("sam_params", "image")
    
    FUNCTION = "extract_sam_data"
    CATEGORY = NAME

    def extract_sam_data(self, input_node_sam_value):
        if not isinstance(input_node_sam_value, tuple):
            raise ValueError("Invalid SAM data deteceted! Split SAM Data expects output from a PlaiFlow Player Iput Node in SAM mode.")
        if len(input_node_sam_value) > 2:
            raise ValueError("Too many values to unpack for SAM data! Split SAM Data expects output from a PlaiFlow Player Iput Node in SAM mode.")
        return input_node_sam_value[0], input_node_sam_value[1]

```
