# Load Images (Upload) 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_LoadImages`
- Category: `Video Helper Suite 🎥🅥🅗🅢`
- Output node: `False`

The `VHS_LoadImages` node is designed for loading a batch of images from a specified directory. It supports filtering the images by skipping initial ones and selecting every nth image, allowing for customizable batch creation. This node is part of the Video Helper Suite, aimed at facilitating video and image processing tasks.
## Input types
### Required
- **`directory`**
    - Specifies the directory from which images are to be loaded. It is crucial for defining the source of the images for processing.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`image_load_cap`**
    - Limits the number of images to be loaded from the directory. This parameter is useful for controlling the size of the data being processed, especially in cases with large numbers of images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`skip_first_images`**
    - Skips a specified number of initial images in the directory. This allows for starting the image loading process from a specific point within the directory.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`select_every_nth`**
    - Loads every nth image from the directory, providing a way to thin out the dataset or to create a more sparse selection of images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The loaded images, processed and returned as a batch. This output is essential for subsequent image processing or analysis tasks.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The masks associated with the loaded images, if available. Masks are useful for tasks that require background removal or focus on specific parts of images.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`int`**
    - The total number of images loaded by the node. This information can be useful for understanding the size of the processed batch.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: `PreviewImage,Reroute,ScribblePreprocessor,EmptyLatentImage,DWPreprocessor,TilePreprocessor,LineArtPreprocessor,Zoe-DepthMapPreprocessor`


## Source code
```python
class LoadImagesFromDirectoryUpload:
    @classmethod
    def INPUT_TYPES(s):
        input_dir = folder_paths.get_input_directory()
        directories = []
        for item in os.listdir(input_dir):
            if not os.path.isfile(os.path.join(input_dir, item)) and item != "clipspace":
                directories.append(item)
        return {
            "required": {
                "directory": (directories,),
            },
            "optional": {
                "image_load_cap": ("INT", {"default": 0, "min": 0, "step": 1}),
                "skip_first_images": ("INT", {"default": 0, "min": 0, "step": 1}),
                "select_every_nth": ("INT", {"default": 1, "min": 1, "step": 1}),
            }
        }
    
    RETURN_TYPES = ("IMAGE", "MASK", "INT")
    FUNCTION = "load_images"

    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢"

    def load_images(self, directory: str, **kwargs):
        directory = folder_paths.get_annotated_filepath(directory.strip())
        return load_images(directory, **kwargs)
    
    @classmethod
    def IS_CHANGED(s, directory: str, **kwargs):
        directory = folder_paths.get_annotated_filepath(directory.strip())
        return is_changed_load_images(directory, **kwargs)

    @classmethod
    def VALIDATE_INPUTS(s, directory: str, **kwargs):
        directory = folder_paths.get_annotated_filepath(directory.strip())
        return validate_load_images(directory)

```
