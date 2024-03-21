# Prune Outputs 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_PruneOutputs`
- Category: `Video Helper Suite 🎥🅥🅗🅢`
- Output node: `True`

The `VHS_PruneOutputs` node is designed to selectively delete files based on the specified options. It allows for the pruning of intermediate, utility, or all files within specified directories, enhancing file management and storage optimization in video processing workflows.
## Input types
### Required
- **`filenames`**
    - Specifies the filenames to be considered for deletion. The structure indicates a prioritization or categorization of files, which influences the deletion process based on the selected option.
    - Python dtype: `Tuple[List[str], List[str]]`
    - Comfy dtype: `VHS_FILENAMES`
- **`options`**
    - Determines the scope of file deletion, allowing for the selection between intermediate files, intermediate and utility files, or all files. This option directly influences which files are pruned from the specified directories.
    - Python dtype: `List[str]`
    - Comfy dtype: `['Intermediate', 'Intermediate and Utility']`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class PruneOutputs:
    @classmethod
    def INPUT_TYPES(s):
        return {
                "required": {
                    "filenames": ("VHS_FILENAMES",),
                    "options": (["Intermediate", "Intermediate and Utility"],)
                    }
                }

    RETURN_TYPES = ()
    OUTPUT_NODE = True
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢"
    FUNCTION = "prune_outputs"

    def prune_outputs(self, filenames, options):
        assert(len(filenames[1]) <= 3 and len(filenames[1]) >= 2)
        delete_list = []
        if options in ["Intermediate", "Intermediate and Utility", "All"]:
            delete_list += filenames[1][1:-1]
        if options in ["Intermediate and Utility", "All"]:
            delete_list.append(filenames[1][0])
        if options in ["All"]:
            delete_list.append(filenames[1][-1])

        output_dirs = [os.path.abspath("output"), os.path.abspath("temp")]
        for file in delete_list:
            #Check that path is actually an output directory
            if (os.path.commonpath([output_dirs[0], file]) != output_dirs[0]) \
                    and (os.path.commonpath([output_dirs[1], file]) != output_dirs[1]):
                        raise Exception("Tried to prune output from invalid directory: " + file)
            if os.path.exists(file):
                os.remove(file)
        return ()

```
