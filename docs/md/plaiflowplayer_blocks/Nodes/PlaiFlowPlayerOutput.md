# Output Node (PlaiFlow Player)
## Documentation
- Class name: `PlaiFlowPlayerOutput`
- Category: `PlaiFlow Player`
- Output node: `True`

This node is responsible for processing and outputting data in various formats such as images, video paths, strings, or document responses based on the input data type. It dynamically handles different types of data, converting and saving them appropriately, and constructs a UI response that encapsulates the processed data along with its type.
## Input types
### Required
- **`file_name_and_ext`**
    - The name and extension of the file to be processed. This parameter is crucial as it determines the naming convention for saved files or the extraction of file names from paths.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`data_type`**
    - Specifies the type of data being processed (e.g., IMAGE, VIDEO_PATH, STRING, DOCUMENT_RESPONSE), which dictates how the data is handled and formatted for output.
    - Python dtype: `str`
    - Comfy dtype: `['IMAGE', 'VIDEO_PATH', 'STRING', 'DOCUMENT_RESPONSE']`
- **`data`**
    - The actual data to be processed. Depending on the data type, this could be image tensors, video paths, strings, or document responses.
    - Python dtype: `Union[List[Any], Tuple[Any, ...], Any]`
    - Comfy dtype: `*`
## Output types
- **`ui`**
    - A UI response that includes the processed data and its type, formatted for display within the PlaiFlow Player environment.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used with nodes that process various data types like images, video paths, strings, or document responses, to dynamically handle and convert these data into a format suitable for UI display within the PlaiFlow Player environment.
## Source code
```python
class PlaiFlowPlayerOutput:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "file_name_and_ext": ("STRING", {}),
                "data_type": (["IMAGE", "VIDEO_PATH", "STRING", "DOCUMENT_RESPONSE", "OBJECT"],),
                "data": (WILDCARD,),
            }
        }
    
    OUTPUT_NODE = True
    RETURN_TYPES = ()

    FUNCTION = "output"
    CATEGORY = NAME

    @classmethod
    def IS_CHANGED(s, image):
        return float("NaN")
    
    def output(self, file_name_and_ext, data_type, data):

        response = []
        match(data_type):

            case 'IMAGE':
                try:
                    for index, img in enumerate(data):
                        pil_image = tensor2pil(img)
                        file_name, file_ext = os.path.splitext(file_name_and_ext)
                        formatted_file_name = f"{file_name}_{index:04d}{file_ext}"
                        image_path = os.path.join(folder_paths.get_output_directory(), formatted_file_name)
                        pil_image.save(image_path)
                        response.append(formatted_file_name)
                except Exception as e:
                    raise e

            case 'VIDEO_PATH':
                video_path = data[1][1]
                response = os.path.basename(video_path)

            case 'STRING':
                if isinstance(data, list):
                    response = data
                else:
                    response.append(str(data))

            case 'DOCUMENT_RESPONSE':
                if isinstance(data, list):
                    response = data
                else:
                    response.append(str(data))


        ui = { "ui": { "PlaiFlowPlayerOutput": response, "data_type": str(data_type) } }

        from pprint import pprint
        pprint(ui, indent=4)

        return ui

```
