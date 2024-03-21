# Preview Bridge (Image)
## Documentation
- Class name: `PreviewBridge`
- Category: `ImpactPack/Util`
- Output node: `True`

The `PreviewBridge` node is designed to manage and refresh image previews based on unique identifiers. It checks if an image associated with a given ID needs to be refreshed in the cache. If so, it either loads the existing image or generates a new preview, updating the cache accordingly. This process ensures that the most current version of an image is always displayed.
## Input types
### Required
- **`images`**
    - A collection of images to potentially update the preview with. This parameter is crucial for determining whether the current preview is outdated and needs refreshing.
    - Python dtype: `Union[List[torch.Tensor], torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`image`**
    - The current image to be potentially updated. It plays a key role in identifying the specific preview that might need refreshing.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`image`**
    - The pixel data of the image, which is part of the output when an image is loaded or refreshed.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The mask associated with the image, used in certain conditions to modify the image preview.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`ui`**
    - The updated image preview to be displayed.
## Usage tips
- Infra type: `CPU`
- Common nodes: `ACN_AdvancedControlNetApply,UltimateSDUpscale,SAMDetectorCombined,DetailerForEachDebug,BboxDetectorSEGS`


## Source code
```python
class PreviewBridge:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "images": ("IMAGE",),
                    "image": ("STRING", {"default": ""}),
                    },
                "hidden": {"unique_id": "UNIQUE_ID"},
                }

    RETURN_TYPES = ("IMAGE", "MASK", )

    FUNCTION = "doit"

    OUTPUT_NODE = True

    CATEGORY = "ImpactPack/Util"

    def __init__(self):
        super().__init__()
        self.output_dir = folder_paths.get_temp_directory()
        self.type = "temp"
        self.prev_hash = None

    @staticmethod
    def load_image(pb_id):
        is_fail = False
        if pb_id not in core.preview_bridge_image_id_map:
            is_fail = True

        image_path, ui_item = core.preview_bridge_image_id_map[pb_id]

        if not os.path.isfile(image_path):
            is_fail = True

        if not is_fail:
            i = Image.open(image_path)
            i = ImageOps.exif_transpose(i)
            image = i.convert("RGB")
            image = np.array(image).astype(np.float32) / 255.0
            image = torch.from_numpy(image)[None,]

            if 'A' in i.getbands():
                mask = np.array(i.getchannel('A')).astype(np.float32) / 255.0
                mask = 1. - torch.from_numpy(mask)
            else:
                mask = torch.zeros((64, 64), dtype=torch.float32, device="cpu")
        else:
            image = empty_pil_tensor()
            mask = torch.zeros((64, 64), dtype=torch.float32, device="cpu")
            ui_item = {
                "filename": 'empty.png',
                "subfolder": '',
                "type": 'temp'
            }

        return image, mask.unsqueeze(0), ui_item

    def doit(self, images, image, unique_id):
        need_refresh = False

        if unique_id not in core.preview_bridge_cache:
            need_refresh = True

        elif core.preview_bridge_cache[unique_id][0] is not images:
            need_refresh = True

        if not need_refresh:
            pixels, mask, path_item = PreviewBridge.load_image(image)
            image = [path_item]
        else:
            res = nodes.PreviewImage().save_images(images, filename_prefix="PreviewBridge/PB-")
            image2 = res['ui']['images']
            pixels = images
            mask = torch.zeros((64, 64), dtype=torch.float32, device="cpu")

            path = os.path.join(folder_paths.get_temp_directory(), 'PreviewBridge', image2[0]['filename'])
            core.set_previewbridge_image(unique_id, path, image2[0])
            core.preview_bridge_image_id_map[image] = (path, image2[0])
            core.preview_bridge_image_name_map[unique_id, path] = (image, image2[0])
            core.preview_bridge_cache[unique_id] = (images, image2)

            image = image2

        return {
            "ui": {"images": image},
            "result": (pixels, mask, ),
        }

```
