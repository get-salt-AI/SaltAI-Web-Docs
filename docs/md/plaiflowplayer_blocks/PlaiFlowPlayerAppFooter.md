# App Footer (PlaiFlow Player)
## Documentation
- Class name: `PlaiFlowPlayerAppFooter`
- Category: `PlaiFlow Player`
- Output node: `False`

This node is responsible for generating the footer content of the PlaiFlow Player application. It takes a string input representing the footer content and outputs a UI component that displays this content at the bottom of the application.
## Input types
### Required
- **`footer_content`**
    - The footer content to be displayed below the app. This string can be multiline, allowing for more detailed footer information or acknowledgments.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`ui`**
    - The UI component that represents the footer content to be displayed at the bottom of the PlaiFlow Player application.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used to customize the user interface by adding a personalized footer to the PlaiFlow Player application, taking a multiline string as input to display detailed footer information or acknowledgments at the bottom of the app.
## Source code
```python
class PlaiFlowPlayerAppFooter:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "footer_content": ("STRING", {
                    "multiline": True, 
                    "dynamicPrompts": False, 
                    "placeholder": "The footer content displayed below the app"
                }),
            }
        }
    
    RETURN_TYPES = ()

    FUNCTION = "app_info"
    CATEGORY = NAME

    def app_info(self, **kwargs):
        ui = { "ui": { "PlaiFlowPlayerAppOutput": { "footer_content": kwargs['footer_content'] } } }

        from pprint import pprint
        pprint(ui, indent=4)

        return ui

```
