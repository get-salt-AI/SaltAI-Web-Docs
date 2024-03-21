# Preview Bridge (Latent)
## Documentation
- Class name: `PreviewBridgeLatent`
- Category: `ImpactPack/Util`
- Output node: `True`

The `PreviewBridgeLatent` node is designed to handle the conversion of latent representations into preview images. It utilizes various decoding methods based on the provided preview method and optional VAE options to generate images from latent tensors. This process involves selecting appropriate decoders or previewers, potentially resizing images, and applying noise masks if present, to produce the final preview image.
## Input types
### Required
- **`latent`**
    - The latent tensor representation that needs to be converted into an image. Its format and content can significantly influence the decoding process and the final image quality.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `STRING`
- **`preview_method`**
    - Specifies the method to be used for decoding the latent tensor into an image. This choice determines which decoder or previewer is utilized and affects the final image appearance.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`vae_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `VAE`
## Output types
- **`latent`**
    - The latent representation of the image, which may be modified or utilized in further processing steps.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`mask`**
    - A mask that may be applied to the image for various effects, such as noise reduction or focus enhancement.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`ui`**
    - The UI component containing the generated preview image, along with additional information such as filename and path. It facilitates the display and management of the image in the user interface.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class PreviewBridgeLatent:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "latent": ("LATENT",),
                    "image": ("STRING", {"default": ""}),
                    "preview_method": (["Latent2RGB-SDXL", "Latent2RGB-SD15", "TAESDXL", "TAESD15"],),
                    },
                "optional": {
                    "vae_opt": ("VAE", )
                },
                "hidden": {"unique_id": "UNIQUE_ID"},
                }

    RETURN_TYPES = ("LATENT", "MASK", )

    FUNCTION = "doit"

    OUTPUT_NODE = True

    CATEGORY = "ImpactPack/Util"

    def __init__(self):
        super().__init__()
        self.output_dir = folder_paths.get_temp_directory()
        self.type = "temp"
        self.prev_hash = None
        self.prefix_append = "_temp_" + ''.join(random.choice("abcdefghijklmnopqrstupvxyz") for x in range(5))

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
                mask = None
        else:
            image = empty_pil_tensor()
            mask = None
            ui_item = {
                "filename": 'empty.png',
                "subfolder": '',
                "type": 'temp'
            }

        return image, mask, ui_item

    def doit(self, latent, image, preview_method, vae_opt=None, unique_id=None):
        need_refresh = False

        if unique_id not in core.preview_bridge_cache:
            need_refresh = True

        elif (core.preview_bridge_cache[unique_id][0] is not latent
              or (vae_opt is None and core.preview_bridge_cache[unique_id][2] is not None)
              or (vae_opt is None and core.preview_bridge_cache[unique_id][1] != preview_method)
              or (vae_opt is not None and core.preview_bridge_cache[unique_id][2] is not vae_opt)):
            need_refresh = True

        if not need_refresh:
            pixels, mask, path_item = PreviewBridge.load_image(image)

            if mask is None:
                mask = torch.ones(latent['samples'].shape[2:], dtype=torch.float32, device="cpu").unsqueeze(0)
                if 'noise_mask' in latent:
                    res_latent = latent.copy()
                    del res_latent['noise_mask']
                else:
                    res_latent = latent
            else:
                res_latent = latent.copy()
                res_latent['noise_mask'] = mask

            res_image = [path_item]
        else:
            decoded_image = decode_latent(latent, preview_method, vae_opt)

            if 'noise_mask' in latent:
                mask = latent['noise_mask']

                decoded_pil = to_pil(decoded_image)

                inverted_mask = 1 - mask  # invert
                resized_mask = resize_mask(inverted_mask, (decoded_image.shape[1], decoded_image.shape[2]))
                result_pil = apply_mask_alpha_to_pil(decoded_pil, resized_mask)

                full_output_folder, filename, counter, _, _ = folder_paths.get_save_image_path("PreviewBridge/PBL-"+self.prefix_append, folder_paths.get_temp_directory(), result_pil.size[0], result_pil.size[1])
                file = f"{filename}_{counter}.png"
                result_pil.save(os.path.join(full_output_folder, file), compress_level=4)
                res_image = [{
                                'filename': file,
                                'subfolder': 'PreviewBridge',
                                'type': 'temp',
                            }]
            else:
                mask = torch.ones(latent['samples'].shape[2:], dtype=torch.float32, device="cpu").unsqueeze(0)
                res = nodes.PreviewImage().save_images(decoded_image, filename_prefix="PreviewBridge/PBL-")
                res_image = res['ui']['images']

            path = os.path.join(folder_paths.get_temp_directory(), 'PreviewBridge', res_image[0]['filename'])
            core.set_previewbridge_image(unique_id, path, res_image[0])
            core.preview_bridge_image_id_map[image] = (path, res_image[0])
            core.preview_bridge_image_name_map[unique_id, path] = (image, res_image[0])
            core.preview_bridge_cache[unique_id] = (latent, preview_method, vae_opt, res_image)

            res_latent = latent

        return {
            "ui": {"images": res_image},
            "result": (res_latent, mask, ),
        }

```
