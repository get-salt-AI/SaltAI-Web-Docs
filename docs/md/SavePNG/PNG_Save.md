# PNG Save Node
## Documentation
- Class name: `PNG_Save`
- Category: `Ebsynth Nodes`
- Output node: `True`

This node processes an image or a batch of images and saves them as PNG files to a specified location. It supports adjusting the image quality, deciding whether to overwrite existing files, and automatically handles the creation of necessary directories. The node utilizes torchvision's encode_png for encoding images.
## Input types
- **`save_path`**
    - Specifies the directory where the PNG files will be saved. It defaults to a predefined output directory.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`file_name`**
    - The base name for the saved PNG files. A default name is provided, but it can be customized.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`images`**
    - The image or batch of images to be saved. The node handles both single images and batches.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`quality`**
    - Determines the quality of the saved PNG images. It is adjustable and has a default value set.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`overwrite_mode`**
    - Controls whether existing files with the same name should be overwritten. It is enabled by default.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The PNG_Save node is primarily utilized for saving processed images or a batch of images as PNG files to a designated location, with options for adjusting image quality and deciding on file overwrite behavior. It is often used at the end of AI pipelines to output the final image generation, ensuring the results are stored with the desired quality and naming conventions.
## Source code
```python
class PNG_Save:
    """
    PNG Save Node

    This node takes in an image and saves it to the specified location using the PNG encoding provided by torchvision's encode_png.
    """

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "save_path": ("STRING", {"default": "./ComfyUI/output"}),
                "file_name": ("STRING", {"default": "ComfyUI"}),
                "images": ("IMAGE",),
                "quality": ("INT", {"default": 96, "min": 0, "max": 100, "step": 1}),
                "overwrite_mode": ("BOOLEAN", {"default": True}),
            }
        }

    RETURN_TYPES = ()
    FUNCTION = "save_image"
    OUTPUT_NODE = True
    CATEGORY = "Ebsynth Nodes"

    def save_image(self, save_path, file_name, images, quality, overwrite_mode=True):
        # If the images tensor has 4 dimensions, it's a batch. Otherwise, make it a batch of 1.
        if len(images.shape) == 3:
            images = images.unsqueeze(0)

        for idx, image in enumerate(images):
            # Construct full path for each image in the batch
            base_name, ext = os.path.splitext(file_name)
            # Ensure the extension is .png
            ext = ".png"
            current_file_name = f"{base_name}{ext}"
            full_path = os.path.join(save_path, current_file_name)

            # Ensure the directory exists
            directory = os.path.dirname(full_path)
            os.makedirs(directory, exist_ok=True)

            # Ensure the image tensor has the correct shape
            image_tensor = image.permute(2, 0, 1).float()  # Change to (C, H, W) format
            C, H, W = image_tensor.shape

            if C != 1 and C != 3:
                raise ValueError(f"Image tensor has {C} channels. Expected 1 or 3.")

            # Convert and encode the image to PNG
            image_tensor = image_tensor.mul(255).clamp(0, 255).type(torch.uint8)
            encoded_image = encode_png(image_tensor.cpu()).numpy().tobytes()

            # Check if file exists and overwrite mode is False
            if os.path.exists(full_path) and not overwrite_mode:
                print(f"File {full_path} already exists and overwrite mode is disabled.")
                continue

            # Save the encoded image
            with open(full_path, 'wb') as f:
                f.write(encoded_image)

            print(f"Image saved to: {full_path}")
                
        return {"status": "Image(s) saved successfully"}

```
