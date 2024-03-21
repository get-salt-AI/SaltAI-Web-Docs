# SaveAnimatedWEBP
## Documentation
- Class name: `SaveAnimatedWEBP`
- Category: `image/animation`
- Output node: `True`

This node is responsible for saving a sequence of PIL images as an animated WEBP file. It iterates through given frames, compiles them into a single WEBP file with specified animation settings such as frame duration, quality, and losslessness, and returns metadata about the saved file.
## Input types
### Required
- **`images`**
    - A list of PIL Image objects to be saved as an animated WEBP. These images represent the frames of the animation.
    - Python dtype: `List[PIL.Image.Image]`
    - Comfy dtype: `IMAGE`
- **`filename_prefix`**
    - The base name for the output file. The node appends a counter to this base name for each frame sequence saved.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`fps`**
    - Frames per second for the animation. This affects the duration each frame is displayed in the animated WEBP.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`lossless`**
    - Determines whether the WEBP animation should be saved in a lossless format. Affects the file size and quality of the animation.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`quality`**
    - The compression quality for the WEBP file, ranging from 0 (worst) to 100 (best). Higher values result in better image quality but larger file sizes.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`method`**
    - The encoding method to be used for generating the WEBP file. Different methods can affect the encoding speed and file size.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`ui`**
    - Returns a UI component containing the list of saved files with their metadata, and a flag indicating if the output is animated.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SaveAnimatedWEBP:
    def __init__(self):
        self.output_dir = folder_paths.get_output_directory()
        self.type = "output"
        self.prefix_append = ""

    methods = {"default": 4, "fastest": 0, "slowest": 6}
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"images": ("IMAGE", ),
                     "filename_prefix": ("STRING", {"default": "ComfyUI"}),
                     "fps": ("FLOAT", {"default": 6.0, "min": 0.01, "max": 1000.0, "step": 0.01}),
                     "lossless": ("BOOLEAN", {"default": True}),
                     "quality": ("INT", {"default": 80, "min": 0, "max": 100}),
                     "method": (list(s.methods.keys()),),
                     # "num_frames": ("INT", {"default": 0, "min": 0, "max": 8192}),
                     },
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"},
                }

    RETURN_TYPES = ()
    FUNCTION = "save_images"

    OUTPUT_NODE = True

    CATEGORY = "image/animation"

    def save_images(self, images, fps, filename_prefix, lossless, quality, method, num_frames=0, prompt=None, extra_pnginfo=None):
        method = self.methods.get(method)
        filename_prefix += self.prefix_append
        full_output_folder, filename, counter, subfolder, filename_prefix = folder_paths.get_save_image_path(filename_prefix, self.output_dir, images[0].shape[1], images[0].shape[0])
        results = list()
        pil_images = []
        for image in images:
            i = 255. * image.cpu().numpy()
            img = Image.fromarray(np.clip(i, 0, 255).astype(np.uint8))
            pil_images.append(img)

        metadata = pil_images[0].getexif()
        if not args.disable_metadata:
            if prompt is not None:
                metadata[0x0110] = "prompt:{}".format(json.dumps(prompt))
            if extra_pnginfo is not None:
                inital_exif = 0x010f
                for x in extra_pnginfo:
                    metadata[inital_exif] = "{}:{}".format(x, json.dumps(extra_pnginfo[x]))
                    inital_exif -= 1

        if num_frames == 0:
            num_frames = len(pil_images)

        c = len(pil_images)
        for i in range(0, c, num_frames):
            file = f"{filename}_{counter:05}_.webp"
            pil_images[i].save(os.path.join(full_output_folder, file), save_all=True, duration=int(1000.0/fps), append_images=pil_images[i + 1:i + num_frames], exif=metadata, lossless=lossless, quality=quality, method=method)
            results.append({
                "filename": file,
                "subfolder": subfolder,
                "type": self.type
            })
            counter += 1

        animated = num_frames != 1
        return { "ui": { "images": results, "animated": (animated,) } }

```