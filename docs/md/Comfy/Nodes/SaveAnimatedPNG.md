# SaveAnimatedPNG
## Documentation
- Class name: `SaveAnimatedPNG`
- Category: `image/animation`
- Output node: `True`

This node is responsible for saving a sequence of images as an animated PNG file. It iterates through a collection of PIL images, saving them together in a single file with specified animation settings such as frame duration, quality, and whether the animation should be lossless. Metadata can also be included in the saved file.
## Input types
### Required
- **`images`**
    - The sequence of images to be saved as an animated PNG. This parameter is crucial as it directly contains the visual content that will be animated.
    - Python dtype: `List[PIL.Image.Image]`
    - Comfy dtype: `IMAGE`
- **`filename_prefix`**
    - The prefix for the output file names. This parameter helps in organizing and identifying the output files easily.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`fps`**
    - Frames per second for the animation, determining the playback speed of the animated PNG. A higher value results in a faster animation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`compress_level`**
    - The compression level for the PNG files, affecting the file size and quality. A higher value results in better compression but might affect image quality.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`ui`**
    - The UI representation of the saved animated PNG, including a list of generated files and whether the animation is single-frame or multi-frame.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SaveAnimatedPNG:
    def __init__(self):
        self.output_dir = folder_paths.get_output_directory()
        self.type = "output"
        self.prefix_append = ""

    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"images": ("IMAGE", ),
                     "filename_prefix": ("STRING", {"default": "ComfyUI"}),
                     "fps": ("FLOAT", {"default": 6.0, "min": 0.01, "max": 1000.0, "step": 0.01}),
                     "compress_level": ("INT", {"default": 4, "min": 0, "max": 9})
                     },
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"},
                }

    RETURN_TYPES = ()
    FUNCTION = "save_images"

    OUTPUT_NODE = True

    CATEGORY = "image/animation"

    def save_images(self, images, fps, compress_level, filename_prefix="ComfyUI", prompt=None, extra_pnginfo=None):
        filename_prefix += self.prefix_append
        full_output_folder, filename, counter, subfolder, filename_prefix = folder_paths.get_save_image_path(filename_prefix, self.output_dir, images[0].shape[1], images[0].shape[0])
        results = list()
        pil_images = []
        for image in images:
            i = 255. * image.cpu().numpy()
            img = Image.fromarray(np.clip(i, 0, 255).astype(np.uint8))
            pil_images.append(img)

        metadata = None
        if not args.disable_metadata:
            metadata = PngInfo()
            if prompt is not None:
                metadata.add(b"comf", "prompt".encode("latin-1", "strict") + b"\0" + json.dumps(prompt).encode("latin-1", "strict"), after_idat=True)
            if extra_pnginfo is not None:
                for x in extra_pnginfo:
                    metadata.add(b"comf", x.encode("latin-1", "strict") + b"\0" + json.dumps(extra_pnginfo[x]).encode("latin-1", "strict"), after_idat=True)

        file = f"{filename}_{counter:05}_.png"
        pil_images[0].save(os.path.join(full_output_folder, file), pnginfo=metadata, compress_level=compress_level, save_all=True, duration=int(1000.0/fps), append_images=pil_images[1:])
        results.append({
            "filename": file,
            "subfolder": subfolder,
            "type": self.type
        })

        return { "ui": { "images": results, "animated": (True,)} }

```
