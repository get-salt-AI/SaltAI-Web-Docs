---
tags:
- LLM
- LlamaIndex
---

# ∞ Simple Directory
## Documentation
- Class name: `LLMDirectoryReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMDirectoryReader node is designed to facilitate the reading of directories, enabling the processing and indexing of documents contained within them. It abstracts the complexity of handling various file types and structures within a directory, making it easier to integrate directory-based data into the Llama Index ecosystem.
## Input types
### Required
- **`input_directory`**
    - Specifies the path to the directory to be read. This path is crucial for determining the scope of files to be processed.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`optional_path_list`**
    - A list of specific paths within the directory to be read. This allows for selective reading of files, enhancing flexibility.
    - Comfy dtype: `*`
    - Python dtype: `List[str]`
- **`recursive`**
    - Determines whether the reading process should include subdirectories. Enabling this allows for a comprehensive indexing of nested directory structures.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`required_ext_list`**
    - A list of file extensions to include in the reading process. This filters the files to be processed based on their extension, tailoring the indexing to specific file types.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`exclude_glob_list`**
    - A list of patterns to exclude from the reading process. This provides a mechanism to omit files that match certain criteria, refining the selection of documents.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The output is a collection of documents that have been read and processed from the specified directory. This includes parsing and indexing the content of files according to the given parameters.
    - Python dtype: `List[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMDirectoryReader:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "input_directory": ("STRING", {}),
            },
            "optional": {
                "optional_path_list": (WILDCARD, {}),
                "recursive": ("BOOLEAN", {"default": False}),
                "required_ext_list": ("STRING", {"default": ".json, .txt, .html"}),
                "exclude_glob_list": ("STRING", {"default": ".sqlite, .zip"}),
            },
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "read_directory"
    CATEGORY = "SALT/Llama-Index/Readers"

    def read_directory(self, input_directory, optional_path_list=[], recursive=False, required_ext_list=None, exclude_glob_list=None):

        if required_ext_list.strip():
            required_exts = [ext.strip() for ext in required_ext_list.split(",") if ext.strip()]
        else:
            required_exts = None

        if exclude_glob_list.strip():
            exclude = [pattern.strip() for pattern in exclude_glob_list.split(",") if pattern.strip()]
        else:
            exclude = None

        print("Excluding: ", exclude)
        print("Required Extensions: ", required_exts)

        if not optional_path_list:
            full_path = get_full_path(1, input_directory.strip())
            input_dir = full_path if os.path.isdir(full_path) else None
            if not input_dir:
                raise ValueError("The provided subdirectory does not exist.")

            reader = SimpleDirectoryReader(
                input_dir=input_dir,
                exclude_hidden=True,
                recursive=recursive,
                required_exts=required_exts,
                exclude=exclude
            )
        elif optional_path_list and isinstance(optional_path_list, (str, list)):

            if isinstance(optional_path_list, str):
                path_list = [optional_path_list]
            else:
                path_list = []
                for path in optional_path_list:
                    if os.path.isfile(path): # and path.startswith(folder_paths.get_input_directory()):
                        path_list.append(path)

            reader = SimpleDirectoryReader(
                input_files=path_list,
            )


        documents = reader.load_data()
        if not documents:
            raise ValueError("No documents found in the specified directory.")

        return (documents,)

```
