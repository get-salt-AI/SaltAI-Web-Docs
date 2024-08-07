---
tags:
- Audio
- SaltNodes
---

# Load Images from ZIP
## Documentation
- Class name: `SaltLoadImageZip`
- Category: `SALT/Image/Loaders`
- Output node: `False`

This node is designed to load images from a ZIP file, optionally resizing them to match the dimensions of the first image encountered. It supports a variety of image formats and is capable of handling multiple images within a single ZIP archive, making it suitable for batch processing of images for further analysis or manipulation.
## Input types
### Required
- **`path`**
    - Specifies the file path to the ZIP archive containing the images to be loaded. It is crucial for locating and accessing the desired ZIP file.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`resize_images_to_first`**
    - Determines whether all loaded images should be resized to match the dimensions of the first image found in the ZIP file. This is useful for ensuring uniformity in image sizes for batch processing.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`images`**
    - Comfy dtype: `IMAGE`
    - Returns a tensor containing the loaded (and possibly resized) images from the ZIP file, ready for further processing or analysis.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SaltLoadImageZip:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "path": ("STRING", {}),
                "resize_images_to_first": ("BOOLEAN", {"default": True})
            }
        }
    
    RETURN_TYPES = ("IMAGE",)
    RETURN_NAMES = ("images",)

    CATEGORY = "SALT/Image/Loaders"
    FUNCTION = "load_images"

    def load_images(self, path: str, resize_images_to_first: bool = True):
        supported_formats = ('.png', '.jpg', '.jpeg', '.gif', '.tga', '.tiff', '.webp')
        images = []
        first_image_size = None
        
        with zipfile.ZipFile(path, 'r') as z:
            for file_name in z.namelist():
                if file_name.lower().endswith(supported_formats):
                    with z.open(file_name) as file:
                        image = Image.open(BytesIO(file.read()))
                        if first_image_size is None:
                            first_image_size = image.size
                        if image.size == first_image_size or resize_images_to_first:
                            images.append(image if image.size == first_image_size else self.resize_right(image, first_image_size))

        if not images:
            errmsg = f"The input zip `{path}` does not contain any valid images!"
            logger.error(errmsg)
            raise ValueError(errmsg)

        images = [pil2tensor(img) for img in images]
        images = torch.cat(images, dim=0)

        return (images, )

    def resize_right(self, image, target_size):
        img_ratio = image.width / image.height
        target_ratio = target_size[0] / target_size[1]
        resize_width, resize_height = (
            (target_size[0], round(target_size[0] / img_ratio)) if target_ratio > img_ratio else
            (round(target_size[1] * img_ratio), target_size[1])
        )
        image = image.resize((resize_width, resize_height), Image.Resampling.LANCZOS)
        x_crop, y_crop = (resize_width - target_size[0]) // 2, (resize_height - target_size[1]) // 2
        return image.crop((x_crop, y_crop, x_crop + target_size[0], y_crop + target_size[1]))

```
