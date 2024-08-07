# CreateRequestMetadata
## Documentation
- Class name: `CreateRequestMetadata`
- Category: `Bmad/api`
- Output node: `True`

The CreateRequestMetadata node is designed for managing metadata related to request processing within a system. It facilitates the creation, update, and retrieval of request metadata, including the addition of resources to a request's metadata file. This node plays a crucial role in tracking the state and outputs of requests, ensuring that each request's information is accurately recorded and maintained throughout its lifecycle.
## Input types
### Required
- **`request_id`**
    - The unique identifier for a request. It is crucial for distinguishing between different requests and for tracking the specific metadata associated with each request.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CreateRequestMetadata:
    """
    Creates a json file with information pertaining to the prompt request.
    Also implements static methods to access and modify this json.
    There should only be ONE instance of this node in a prompt.
    """

    request_id = None
    output_dir = ""

    def __init__(self):
        self.type = "output"

    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
            "request_id": ("STRING", {"default": "insert_id"})
        },
        }

    RETURN_TYPES = ()
    FUNCTION = "update_outdata"
    CATEGORY = api_category_path
    OUTPUT_NODE = True

    @staticmethod
    def get_and_validate_requestID():
        if CreateRequestMetadata.request_id is None:
            raise TypeError("Request ID was not set. CreateRequestMetadata node might be missing.")
        if CreateRequestMetadata.request_id == "":
            raise ValueError("Request ID was set to empty."
                             " Check if it is being properly set to avoid conflicts with subsequent requests.")
        return CreateRequestMetadata.request_id

    @staticmethod
    def get_request_status_file_name():
        return f"{CreateRequestMetadata.request_id}.json"

    @staticmethod
    def get_request_status_file_path():
        file = CreateRequestMetadata.get_request_status_file_name()
        filename = os.path.join(CreateRequestMetadata.output_dir, file)
        return filename

    @staticmethod
    def get_request_metadata():
        filename = CreateRequestMetadata.get_request_status_file_path()
        try:
            with open(filename, 'r') as f:
                data = json.load(f)
        except FileNotFoundError:
            print(f"\033[91mMetadata file not found: {filename}\033[00m")
            return None
        return data

    @staticmethod
    def add_resource(resource_name, resource_filename):
        filename = CreateRequestMetadata.get_request_status_file_path()
        try:
            with open(filename, 'r') as f:
                data = json.load(f)
        except FileNotFoundError:
            print(f"\033[91mMetadata file not found: {filename}\033[00m")
            return

        resource_index = next((index for index, value in enumerate(data["outputs"]) if value["name"] == resource_name),
                              -1)
        resource_already_registered = resource_index != -1

        if not resource_already_registered:
            data["outputs"].append({"name": resource_name, "resource": [resource_filename]})
        else:
            data["outputs"][resource_index]["resource"].append(resource_filename)

        with open(filename, 'w') as f:
            json.dump(data, f)

    @staticmethod
    def add_resource_list(resource_name, resource_filenames):
        filename = CreateRequestMetadata.get_request_status_file_path()
        try:
            with open(filename, 'r') as f:
                data = json.load(f)
        except FileNotFoundError:
            print(f"\033[91mMetadata file not found: {filename}\033[00m")
            return

        resource_index = next((index for index, value in enumerate(data["outputs"]) if value["name"] == resource_name),
                              -1)
        resource_already_registered = resource_index != -1

        if not resource_already_registered:
            data["outputs"].append({"name": resource_name, "resource": resource_filenames})
        else:
            data["outputs"][resource_index]["resource"].extend(resource_filenames)

        with open(filename, 'w') as f:
            json.dump(data, f)

    @staticmethod
    def update_request_state(state):
        filename = CreateRequestMetadata.get_request_status_file_path()
        try:
            with open(filename, 'r') as f:
                data = json.load(f)
        except FileNotFoundError:
            print(f"\033[91mMetadata file not found: {filename}\033[00m")
            return

        data["state"] = "complete"
        with open(filename, 'w') as f:
            json.dump(data, f)

    @staticmethod
    def set_error_state(message):
        filename = CreateRequestMetadata.get_request_status_file_path()
        try:
            with open(filename, 'r') as f:
                data = json.load(f)
        except FileNotFoundError:
            print(f"\033[91mMetadata file not found: {filename}\033[00m")
            return

        data["state"] = "failed"
        data["error"] = message

        with open(filename, 'w') as f:
            json.dump(data, f)

        raise Exception(message)

    def update_outdata(self, request_id):
        if request_id == "insert_id":
            raise ValueError("Request ID in CreateRequestMetadata node with value: "
                             "'insert_id'. You might not be setting it properly or "
                             "might have more than one CreateRequestMetadata node in your workflow/node.")

        assert CreateRequestMetadata.request_id != request_id, (
            "Request ID is equal to previously set ID. "
            "You may have more than one CreateRequestMetadata node in your workflow/prompt.")

        # no problems found, set the request id
        CreateRequestMetadata.request_id = request_id
        CreateRequestMetadata.output_dir = folder_paths.get_output_directory()

        # get output path
        filename = CreateRequestMetadata.get_request_status_file_path()

        # write request status to json file
        request_info = {"state": "started", "outputs": []}
        with open(filename, 'w') as f:
            json.dump(request_info, f)

        return ()

```
