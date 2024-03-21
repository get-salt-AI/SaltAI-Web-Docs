# Input Node (PlaiFlow Player)
## Documentation
- Class name: `PlaiFlowPlayerInput`
- Category: `PlaiFlow Player`
- Output node: `True`

The `PlaiFlowPlayerInput` node is designed to handle various types of input data for the PlaiFlow Player. It supports a wide range of data types including strings, numbers, images, and more, allowing for flexible data input handling within the PlaiFlow Player environment.
## Input types
### Required
- **`field_name`**
    - Specifies the name of the input field. This is crucial for identifying and processing the input data correctly within the node.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`field_desc`**
    - Provides a description for the input field, enhancing the understanding and context of the data being processed.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`data_type`**
    - Determines the type of the input data, supporting a variety of formats such as strings, floats, integers, images, and more. This flexibility allows the node to handle a wide range of input data types.
    - Python dtype: `Union[str, float, int, Any]`
    - Comfy dtype: `['STRING', 'FLOAT', 'INT', 'IMAGE', 'IMAGES', 'MASK', 'SAM', 'DOCUMENT', 'SELECTION']`
- **`field_value`**
    - Contains the actual value of the input data, which is processed based on the specified data type.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`image`**
    - Optional parameter for providing an image as input. This is used when the input data type is specifically an image.
    - Python dtype: `Any`
    - Comfy dtype: `IMAGE`
- **`base64_image`**
    - Optional parameter for providing an image in base64 encoded string format. This allows for image data to be transmitted and processed as a string.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`*`**
    - The processed output value of the input data, which can be of any type depending on the input data type specified.
    - Python dtype: `Any`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used to provide a variety of input data types, including strings, numbers, images, and more, to the PlaiFlow Player, allowing for flexible data input handling within AI pipelines.
## Source code
The node code is private
