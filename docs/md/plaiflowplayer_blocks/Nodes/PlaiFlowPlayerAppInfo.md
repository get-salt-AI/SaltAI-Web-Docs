# App Info (PlaiFlow Player)
## Documentation
- Class name: `PlaiFlowPlayerAppInfo`
- Category: `PlaiFlow Player`
- Output node: `False`

This node is responsible for generating the application information UI, including the title and description of the application. It takes the title and description as inputs and constructs a UI element that displays this information.
## Input types
### Required
- **`title`**
    - The title of the application. It is a crucial element as it represents the application's identity and is prominently displayed in the app info UI.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`description`**
    - The description of the application. It provides users with more context about the application's purpose and functionality, enhancing the app info UI.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`ui`**
    - The constructed UI element that displays the application's title and description.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used at the beginning of a pipeline to provide users with an overview of the application, taking the application's title and description as inputs and generating a UI element that displays this information.
## Source code
The node code is private
