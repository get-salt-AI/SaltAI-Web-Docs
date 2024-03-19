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
```python
class PlaiFlowPlayerInput:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "field_name": ("STRING", {}),
                "field_desc": ("STRING", {}),
                "data_type": (["STRING", "FLOAT", "INT", "IMAGE", "IMAGES", "MASK", "SAM", "DOCUMENT", "SELECTION"],),
                "field_value": ("STRING", {"multiline": True, "dynamicPrompts": False}),
            },
            "optional": {
                "image": ("IMAGE",),
                "base64_image": ("STRING", {"multiline": True, "dynamicPrompts": False}),
            }
        }
    
    OUTPUT_NODE = True
    RETURN_TYPES = (WILDCARD, )
    RETURN_NAMES = ("value", )

    FUNCTION = "input"
    CATEGORY = NAME

    def input(self, field_name, field_desc, field_value, data_type, image=None, base64_image=None, file_list=[]):

        if data_type == 'IMAGE':

            src_image = None

            if not image and not base64_image:
                raise ValueError("A image, or base64_image input is required when using data_type `IMAGE`")

            if isinstance(image, torch.Tensor):
                src_image = image
            
            if base64_image:
                try:
                    src_image = pil2tensor(Image.open(BytesIO(base64.b64decode(base64_image))).convert("RGB"))
                except Exception as e:
                    print("There was an error processing the base64 string, is it malformed?")
                    raise e

            return (src_image, )
        
        if data_type == 'MASK':

            src_image = None

            if isinstance(image, torch.Tensor):
                src_image = image
            
            if base64_image:
                try:
                    src_image = pil2mask(Image.open(BytesIO(base64.b64decode(base64_image))).convert("L"))
                except Exception as e:
                    print("There was an error processing the base64 string, is it malformed?")
                    raise e

            return (src_image, )
    
        elif data_type == "IMAGES":

            tensors = []

            if not image and field_value.strip() == "":
                raise ValueError("No image batch provided, or field_value consisting of a UUID")
            
            if isinstance(image, torch.Tensor):
                return (image, )
            
            try:
                uuid_obj = uuid.UUID(field_value, version=4)
            except ValueError as e:
                print("There was an error processing `field_value` as a valid UUID4 hash pointing to a cache location!")
                raise e

            uuid = str(uuid_obj)

            return (load_images(uuid_obj, ['.jpeg', '.jpg', '.png', '.gif', '.webp']), )


        out = ""
        match data_type:

            case 'STRING':

                out = field_value

            case 'INT':

                out = int(field_value)

            case 'FLOAT':

                out = float(field_value)

            case 'SELECTION':

                fields = field_value.splitlines()
                if fields:
                    out = str(fields[0])
                else:
                    raise ValueError("Unable to determine any list values!")

            case 'SAM':

                src_image = None

                if base64_image:
                    try:
                        src_image = pil2tensor(Image.open(BytesIO(base64.b64decode(base64_image))).convert("RGB"))
                    except Exception as e:
                        print("There was an error processing the base64 string, is it malformed?")
                        raise e

                # Parse JSON
                sam_json = json.loads(field_value)

                points_raw = sam_json.get('points', '[128, 128]; [0, 0]')
                points_list = [list(map(int, point.strip('[] ').split(','))) for point in points_raw.split(';')]
                points_array = np.array(points_list)

                labels_list = sam_json.get('labels', [1, 0])
                labels_array = np.array(labels_list)

                sam_params = {
                    "points": points_array,
                    "labels": labels_array
                }

                out = (sam_params, src_image)



            case _:
                
                out = field_value


        print(f"PlaiFlow Player Input `{field_name}` ({data_type}) Value:")
        print(out)

        return (out, )

```
