# Model List (PlaiFlow Player)
## Documentation
- Class name: `PlaiFlowPlayerModelList`
- Category: `PlaiFlow Player`
- Output node: `False`

This node generates a list of files based on a given directory. It is useful for dynamically populating a dropdown or selection list with available files within a specified directory.
## Input types
### Required
- **`combo_list`**
    - The directory from which to generate the file list. This selection is crucial as it determines the scope of files to be included in the output list.
    - Python dtype: `List[str]`
    - Comfy dtype: `['STRING']`
## Output types
- **`*`**
    - A list of files from the specified directory. This output can be used to populate UI elements with file options.
    - Python dtype: `List[str]`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used with UI elements to dynamically populate a dropdown or selection list with available files from a specified directory, based on the user's selection in the `combo_list`.
## Source code
The node code is private
