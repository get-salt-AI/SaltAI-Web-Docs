# Load Images (Path) 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_LoadImagesPath`
- Category: `Video Helper Suite 🎥🅥🅗🅢`
- Output node: `False`

The `VHS_LoadImagesPath` node is designed for loading images from a specified directory path. It supports filtering and selection options such as limiting the number of images loaded, skipping initial images, and selecting every nth image. This functionality is crucial for processing large datasets or specific subsets of images efficiently.
## Input types
### Required
- **`directory`**
    - Specifies the directory from which images are to be loaded. It's a crucial parameter as it determines the source of the images for processing.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`image_load_cap`**
    - Limits the number of images to be loaded from the directory. This is useful for handling large datasets by processing only a subset of images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`skip_first_images`**
    - Skips a specified number of initial images in the directory. This allows for selective processing of images starting from a certain point.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`select_every_nth`**
    - Loads every nth image from the directory, providing a way to sample the dataset or reduce the number of images processed.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The loaded images as a batch. This output is essential for further processing or analysis of the images.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The masks associated with the loaded images, if available. Masks are useful for tasks that require segmentation or specific area processing.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`int`**
    - The total number of images loaded. This information can be useful for tracking dataset size or processing progress.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImpactImageBatchToImageList,PreviewImage,IPAdapterEncoder,LinearBatchCreativeInterpolation`


## Source code
```python
class LoadImagesFromDirectoryPath:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "directory": ("STRING", {"default": "X://path/to/images", "vhs_path_extensions": []}),
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
        if directory is None or validate_load_images(directory) != True:
            raise Exception("directory is not valid: " + directory)

        return load_images(directory, **kwargs)
    
    @classmethod
    def IS_CHANGED(s, directory: str, **kwargs):
        if directory is None:
            return "input"
        return is_changed_load_images(directory, **kwargs)

    @classmethod
    def VALIDATE_INPUTS(s, directory: str, **kwargs):
        if directory is None:
            return True
        return validate_load_images(directory)

```
