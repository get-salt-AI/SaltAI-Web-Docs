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
The node code is private
