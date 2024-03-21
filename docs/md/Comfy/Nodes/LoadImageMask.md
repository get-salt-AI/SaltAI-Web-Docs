# Load Image (as Mask)
## Documentation
- Class name: `LoadImageMask`
- Category: `mask`
- Output node: `False`

The `LoadImageMask` node is designed to load an image from a specified path, process it to handle different image modes and orientations, and extract a mask based on the specified color channel. It supports handling images with alpha channels for mask extraction and ensures images are in the correct orientation and format for further processing.
## Input types
### Required
- **`image`**
    - The `image` parameter specifies the file name of the image to be loaded and processed. It is crucial for identifying the correct file within a predefined directory structure and extracting the relevant mask data for further operations.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`channel`**
    - The `channel` parameter specifies the color channel ('red', 'green', 'blue', 'alpha') from which the mask will be extracted. This allows for flexible mask creation based on different color channels of the image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`mask`**
    - The extracted mask from the specified color channel of the image. Useful for segmentation tasks and further image processing.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LoadImageMask:
    _color_channels = ["alpha", "red", "green", "blue"]
    @classmethod
    def INPUT_TYPES(s):
        input_dir = folder_paths.get_input_directory()
        files = [f for f in os.listdir(input_dir) if os.path.isfile(os.path.join(input_dir, f))]
        return {"required":
                    {"image": (sorted(files), {"image_upload": True}),
                     "channel": (s._color_channels, ), }
                }

    CATEGORY = "mask"

    RETURN_TYPES = ("MASK",)
    FUNCTION = "load_image"
    def load_image(self, image, channel):
        image_path = folder_paths.get_annotated_filepath(image)
        i = Image.open(image_path)
        i = ImageOps.exif_transpose(i)
        if i.getbands() != ("R", "G", "B", "A"):
            if i.mode == 'I':
                i = i.point(lambda i: i * (1 / 255))
            i = i.convert("RGBA")
        mask = None
        c = channel[0].upper()
        if c in i.getbands():
            mask = np.array(i.getchannel(c)).astype(np.float32) / 255.0
            mask = torch.from_numpy(mask)
            if c == 'A':
                mask = 1. - mask
        else:
            mask = torch.zeros((64,64), dtype=torch.float32, device="cpu")
        return (mask.unsqueeze(0),)

    @classmethod
    def IS_CHANGED(s, image, channel):
        image_path = folder_paths.get_annotated_filepath(image)
        m = hashlib.sha256()
        with open(image_path, 'rb') as f:
            m.update(f.read())
        return m.digest().hex()

    @classmethod
    def VALIDATE_INPUTS(s, image):
        if not folder_paths.exists_annotated_filepath(image):
            return "Invalid image file: {}".format(image)

        return True

```
