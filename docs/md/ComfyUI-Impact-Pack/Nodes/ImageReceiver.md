# Image Receiver
## Documentation
- Class name: `ImageReceiver`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImageReceiver` node processes an input image, optionally decodes it from base64 if `save_to_workflow` is true, applies EXIF orientation correction, converts it to an RGB format, and normalizes it. It also generates a mask based on the alpha channel if present, or a zero mask otherwise. This node is designed to prepare images for further processing within a workflow, handling both image data received as base64 strings and direct image inputs.
## Input types
### Required
- **`image`**
    - The primary image input for processing. It can be a direct image input or a base64-encoded string, depending on the `save_to_workflow` flag.
    - Python dtype: `Union[torch.Tensor, str]`
    - Comfy dtype: `IMAGE`
- **`link_id`**
    - An identifier for linking the processed image to other components or outputs in the workflow. Its specific use may vary depending on the workflow design.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`save_to_workflow`**
    - A flag indicating whether the input image is a base64-encoded string that needs to be decoded and processed. When true, additional processing steps are performed.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`image_data`**
    - Optional base64-encoded image data. When `save_to_workflow` is true, this data is decoded and processed as the input image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`trigger_always`**
    - A flag that may control the node's execution behavior in response to workflow triggers, though its specific function is not detailed in the provided context.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`image`**
    - A mask generated based on the presence of an alpha channel in the input image, or a zero mask if no alpha channel is present.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImageReceiver:
    @classmethod
    def INPUT_TYPES(s):
        input_dir = folder_paths.get_input_directory()
        files = [f for f in os.listdir(input_dir) if os.path.isfile(os.path.join(input_dir, f))]
        return {"required": {
                    "image": (sorted(files), ),
                    "link_id": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
                    "save_to_workflow": ("BOOLEAN", {"default": False}),
                    "image_data": ("STRING", {"multiline": False}),
                    "trigger_always": ("BOOLEAN", {"default": False, "label_on": "enable", "label_off": "disable"}),
                    },
                }

    FUNCTION = "doit"

    RETURN_TYPES = ("IMAGE", "MASK")

    CATEGORY = "ImpactPack/Util"

    def doit(self, image, link_id, save_to_workflow, image_data, trigger_always):
        if save_to_workflow:
            try:
                image_data = base64.b64decode(image_data.split(",")[1])
                i = Image.open(BytesIO(image_data))
                i = ImageOps.exif_transpose(i)
                image = i.convert("RGB")
                image = np.array(image).astype(np.float32) / 255.0
                image = torch.from_numpy(image)[None,]
                if 'A' in i.getbands():
                    mask = np.array(i.getchannel('A')).astype(np.float32) / 255.0
                    mask = 1. - torch.from_numpy(mask)
                else:
                    mask = torch.zeros((64, 64), dtype=torch.float32, device="cpu")
                return (image, mask.unsqueeze(0))
            except Exception as e:
                print(f"[WARN] ComfyUI-Impact-Pack: ImageReceiver - invalid 'image_data'")
                mask = torch.zeros((64, 64), dtype=torch.float32, device="cpu")
                return (empty_pil_tensor(64, 64), mask, )
        else:
            return nodes.LoadImage().load_image(image)

    @classmethod
    def VALIDATE_INPUTS(s, image, link_id, save_to_workflow, image_data, trigger_always):
        if image != '#DATA' and not folder_paths.exists_annotated_filepath(image) or image.startswith("/") or ".." in image:
            return "Invalid image file: {}".format(image)

        return True

    @classmethod
    def IS_CHANGED(s, image, link_id, save_to_workflow, image_data, trigger_always):
        if trigger_always:
            return float("NaN")
        else:
            if save_to_workflow:
                return hash(image_data)
            else:
                return hash(image)

```
