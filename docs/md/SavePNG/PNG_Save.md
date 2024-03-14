# PNG Save Node
## Documentation
- Class name: `PNG_Save`
- Category: `Ebsynth Nodes`
- Output node: `True`

The PNG_Save node is designed to take an image or a batch of images and save them as PNG files to a specified location. It supports setting the image quality, deciding whether to overwrite existing files, and automatically handles the conversion of image tensors to the appropriate format for PNG encoding.
## Input types
### Required
- **`save_path`**
    - Specifies the directory where the image(s) will be saved. It allows for organizing output images in a structured manner.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`file_name`**
    - Determines the base name for the output file(s). This can be used to identify and differentiate between saved images.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`images`**
    - The image tensor or a batch of image tensors to be saved. This node handles the conversion and encoding of these tensors into PNG format.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`quality`**
    - Sets the quality of the saved PNG image(s). Higher values result in better image quality but larger file sizes, directly influencing the visual fidelity and the compression level of the output images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`overwrite_mode`**
    - Controls whether existing files with the same name should be overwritten. This is useful for avoiding accidental data loss.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used with image processing and transformation nodes to save the final, processed images as PNG files to a specified location, ensuring the output is readily accessible and in a widely supported format.
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
