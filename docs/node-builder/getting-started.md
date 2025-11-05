This guide will walk you through the basics of using Node Builder and creating your first custom node.

!!! note
    Node Builder is currently in BETA. We are actively working on documenting all of the features and examples.


## Understanding the Interface

<img src="/node-builder/node-builder-examples.png" alt="Node Builder Examples">


Node Builder provides a professional development environment with four main areas:

### 1. File Explorer (Left Panel)

- Displays your project files and folders
- Right-click to add, rename, or delete files
- Default structure includes:
    - `node.py` - Main node implementation
    - `pyproject.toml` - Poetry dependency management
    - Additional custom files as needed

### 2. Code Editor (Center Panel)

- Full-featured Python editor
- Syntax highlighting
- Manual saving with Ctrl/Cmd + S
- ⚠️ Remember to save your changes frequently

> Important: Node Builder does not auto-save your work. Always remember to save your changes using Ctrl/Cmd + S before building or switching away from the editor.
> 

### 3. Shell Panel (Bottom Right)

- Access to your development environment
- Run Poetry commands for dependency management
- Execute Python scripts
- Common commands:
    
    ```bash
    poetry add package-name    # Add a new dependency
    poetry remove package-name # Remove a dependency
    poetry install            # Install all dependencies
    
    ```
    

### 4. Log Panel (Bottom Center)

- Build output and errors
- Runtime logs
- Debugging information

## Creating Your First Node

### Starting a New Node Project

1. Click the "+ New Node" button
2. Choose either:
    - "Start from blank" for a clean slate
    - "Start from example" to use a template

### Basic Node Structure

Every Salt node requires a Python class that follows the ComfyUI schema. Nodes are organized by category in the node menu, with each node specifying a single category for organization. Here's a minimal example:

```python
class ExampleNode:
    """A basic Salt node example showing various widget types."""

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                # Basic text input with placeholder
                "text_input": ("STRING", {
                    "default": "",
                    "placeholder": "Enter text here",
                    "tooltip": "Enter any text to process"
                }),

                # Dropdown selection
                "selection": (["option1", "option2", "option3"], {
                    "default": "option1",
                    "tooltip": "Select from available options"
                }),

                # Integer slider with range
                "number_slider": ("INT", {
                    "default": 50,
                    "min": 0,
                    "max": 100,
                    "step": 1,
                    "tooltip": "Adjust value between 0 and 100"
                }),

                # Float input with precision
                "decimal_number": ("FLOAT", {
                    "default": 1.0,
                    "min": 0.0,
                    "max": 10.0,
                    "step": 0.1,
                    "tooltip": "Enter a decimal number"
                }),

                # Boolean toggle
                "toggle": ("BOOLEAN", {
                    "default": False,
                    "tooltip": "Toggle this option on/off"
                })
            },
            "optional": {
                # Optional text area
                "notes": ("STRING", {
                    "default": "",
                    "multiline": True,
                    "placeholder": "Add optional notes here",
                    "tooltip": "Additional notes (optional)"
                }),

                # Optional image input
                "image": ("IMAGE", {
                    "tooltip": "Upload an image (optional)"
                })
            }
        }

    RETURN_TYPES = ("STRING", "INT", "FLOAT")
    RETURN_NAMES = ("processed_text", "number_result", "decimal_result")

    FUNCTION = "process"
    CATEGORY = "examples"  # Node category in menu

    # Optional node documentation
    DESCRIPTION = "Example node demonstrating various widget types"

    def process(self, text_input, selection, number_slider,
                decimal_number, toggle, notes="", image=None):
        try:
            # Example processing logic
            result_text = f"Processing: {text_input} with {selection}"

            if toggle:
                result_text += " (enabled)"

            if notes:
                result_text += f"\\nNotes: {notes}"

            if image is not None:
                result_text += "\\nImage provided"

            return (
                result_text,
                number_slider,
                decimal_number
            )

        except Exception as e:
            raise ValueError(f"Error processing node: {str(e)}")

```

## Building Your Node

The build process is a critical step that makes your node changes available to your workflows. Without building, your changes won't be accessible in the Workflow tab.

### Build Process

1. Save your changes (Ctrl/Cmd + S)
2. Click the "Build" button in the top right corner
3. A loading spinner will appear while the build is in progress
4. Once complete, you'll see a success message: "Your changes are now available"
5. Switch to the Workflow tab to use your updated node

### Build States

- **Not Built**: Changes exist but haven't been built
- **Building**: Loading spinner indicates build in progress
- **Build Successful**: Green checkmark with "Your changes are now available" message
- **Build Failed**: Error message will appear in the log panel

### Common Build Issues

- Forgetting to save changes before building
- Syntax errors in your Python code
- Missing dependencies
- Incorrect class structure

> Important: Always check the log panel if your build fails. It will contain helpful information about what went wrong.
> 

### Widget Types and Configuration

Salt nodes support a variety of widget types for input:

### Basic Types

- `STRING`: Text input
    
    ```python
    "text_field": ("STRING", {
        "default": "",
        "placeholder": "Enter text",
        "tooltip": "Help text here",
        "multiline": False  # Set to True for text area
    })
    
    ```
    
- `INT`: Integer number input
    
    ```python
    "number": ("INT", {
        "default": 0,
        "min": 0,
        "max": 100,
        "step": 1,  # Increment amount
        "tooltip": "Select a number"
    })
    
    ```
    
- `FLOAT`: Decimal number input
    
    ```python
    "decimal": ("FLOAT", {
        "default": 0.0,
        "min": 0.0,
        "max": 1.0,
        "step": 0.1,
        "tooltip": "Select a decimal number"
    })
    
    ```
    

### Selection Widgets

- Dropdown Selection
    
    ```python
    "choice": (["option1", "option2"], {
        "default": "option1",
        "tooltip": "Select an option"
    })
    
    ```
    
- Boolean Toggle
    
    ```python
    "toggle": ("BOOLEAN", {
        "default": False,
        "tooltip": "Toggle option"
    })
    
    ```
    

### Special Types

- `IMAGE`: Image data input
    
    ```python
    "image": ("IMAGE", {
        "tooltip": "Upload or connect an image"
    })
    
    ```
    

### Widget Features

- **Tooltips**: Add helpful descriptions with `tooltip`
- **Placeholders**: Show example input with `placeholder`
- **Validation**: Set `min`, `max`, and `step` for numbers
- **Multiline**: Enable text areas with `multiline: True`
- **Default Values**: Set initial values with `default`

## Using the Salt Logger

Salt provides a built-in logger utility to help you debug and monitor your nodes. The logger outputs to the Log Panel in the Node Builder interface.

### Basic Logger Usage

First, import the logger in your node:

```python
from salt.node_builder import logger

class MyNode:
    def process(self, input_data):
        try:
            # Basic logging
            logger.info("Processing started")
            logger.warning("Warning message")
            logger.error("Error message")

            # Log data structures
            my_data = {"key": "value"}
            logger.data(my_data)  # Pretty prints data structures

            return (result,)
        except Exception as error:
            logger.error(f"Process failed: {str(error)}")
            raise ValueError(f"Process failed: {str(error)}")

```

### Logger Methods

- `logger.info()`: General information messages
- `logger.warning()`: Warning messages
- `logger.error()`: Error messages
- `logger.data()`: Pretty prints data structures (useful for debugging)

### Advanced Logging Examples

```python
class AdvancedNode:
    def process(self, complex_input):
        try:
            # Log input data
            logger.info("Received input:")
            logger.data(complex_input)

            # Log processing steps
            logger.info("Starting processing step 1")
            # ... processing code ...

            # Log nested data structures
            results = {
                "step1": {"status": "complete"},
                "step2": {"status": "pending"}
            }
            logger.data(results, indent=2)  # Custom indentation

            if some_condition:
                logger.warning("Processing continued with fallback method")

            return (output,)
        except Exception as error:
            logger.error(f"Critical error in processing: {str(error)}")
            raise ValueError(str(error))

```

### Best Practices

1. **Log Important Steps**
    
    ```python
    logger.info("Starting process")
    logger.info(f"Processing {len(items)} items")
    logger.info("Process complete")
    
    ```
    
2. **Debug Data Structures**
    
    ```python
    # Log complex data for debugging
    logger.data({
        "inputs": input_data,
        "parameters": params,
        "results": results
    })
    
    ```
    
3. **Error Handling**
    
    ```python
    try:
        # Your code
    except Exception as e:
        logger.error(f"Operation failed: {str(e)}")
        logger.data({
            "error_type": type(e).__name__,
            "error_details": str(e),
            "current_state": current_state
        })
        raise
    
    ```
    
4. **Progress Tracking**
    
    ```python
    def process_items(self, items):
        logger.info(f"Starting batch process of {len(items)} items")
        for i, item in enumerate(items, 1):
            logger.info(f"Processing item {i}/{len(items)}")
            # ... processing code ...
    
    ```
    

> Tip: Use logger.data() for complex data structures instead of string formatting - it provides better formatting and readability in the log panel.
> 

## Best Practices for New Nodes

1. **Clear Naming**
    - Use descriptive class names
    - Follow Python naming conventions
    - Document your node's purpose
2. **Input Validation**
    - Set appropriate min/max values
    - Provide sensible defaults
    - Validate input types
3. **Documentation**
    - Add docstrings to your class
    - Comment complex logic
    - Explain expected inputs/outputs
4. **Testing**
    - Test with various inputs
    - Handle edge cases
    - Verify error messages


## Next Steps

Once you're comfortable with basic node creation:

1. Explore example nodes
2. Learn about advanced input types
3. Study the ComfyUI schema in detail
4. Practice error handling
5. Experiment with custom node categories

Ready to dive deeper? Continue to the "Working with Nodes" section for advanced node development techniques.
