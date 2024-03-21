# LatentReceiver
## Documentation
- Class name: `LatentReceiver`
- Category: `ImpactPack/Util`
- Output node: `False`

The node 'LatentReceiver' is designed to handle the reception or processing of latent representations in a machine learning or AI context. This could involve operations such as loading, transforming, or transmitting latent vectors.
## Input types
### Required
- **`latent`**
    - The 'latent' input represents the latent representation to be processed. Its role is crucial for the node's operation as it directly influences the manipulation or transformation applied to the latent data.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`link_id`**
    - The 'link_id' parameter is used for identifying the specific connection or link in a network of nodes, facilitating the targeted processing or routing of latent data.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`trigger_always`**
    - The 'trigger_always' parameter determines whether the node should process the latent data unconditionally, ensuring continuous operation regardless of other conditions.
    - Python dtype: `bool`
    - Comfy dtype: `BOOL`
## Output types
- **`latent`**
    - The output 'latent' contains the processed latent representation. This could involve transformations such as scaling, rotation, or other manipulations based on the node's functionality.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LatentReceiver:
    def __init__(self):
        self.input_dir = folder_paths.get_input_directory()
        self.type = "input"

    @classmethod
    def INPUT_TYPES(s):
        def check_file_extension(x):
            return x.endswith(".latent") or x.endswith(".latent.png")

        input_dir = folder_paths.get_input_directory()
        files = [f for f in os.listdir(input_dir) if os.path.isfile(os.path.join(input_dir, f)) and check_file_extension(f)]
        return {"required": {
                    "latent": (sorted(files), ),
                    "link_id": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}),
                    "trigger_always": ("BOOLEAN", {"default": False, "label_on": "enable", "label_off": "disable"}),
                    },
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    RETURN_TYPES = ("LATENT",)

    @staticmethod
    def load_preview_latent(image_path):
        if not os.path.exists(image_path):
            return None

        image = Image.open(image_path)
        exif_data = piexif.load(image.info["exif"])

        if piexif.ExifIFD.UserComment in exif_data["Exif"]:
            compressed_data = exif_data["Exif"][piexif.ExifIFD.UserComment]
            compressed_data_io = BytesIO(compressed_data)
            with zipfile.ZipFile(compressed_data_io, mode='r') as archive:
                tensor_bytes = archive.read("latent")
            tensor = safetensors.torch.load(tensor_bytes)
            return {"samples": tensor['latent_tensor']}
        return None

    def parse_filename(self, filename):
        pattern = r"^(.*)/(.*?)\[(.*)\]\s*$"
        match = re.match(pattern, filename)
        if match:
            subfolder = match.group(1)
            filename = match.group(2).rstrip()
            file_type = match.group(3)
        else:
            subfolder = ''
            file_type = self.type

        return {'filename': filename, 'subfolder': subfolder, 'type': file_type}

    def doit(self, **kwargs):
        if 'latent' not in kwargs:
            return (torch.zeros([1, 4, 8, 8]), )

        latent = kwargs['latent']

        latent_name = latent
        latent_path = folder_paths.get_annotated_filepath(latent_name)

        if latent.endswith(".latent"):
            latent = safetensors.torch.load_file(latent_path, device="cpu")
            multiplier = 1.0
            if "latent_format_version_0" not in latent:
                multiplier = 1.0 / 0.18215
            samples = {"samples": latent["latent_tensor"].float() * multiplier}
        else:
            samples = LatentReceiver.load_preview_latent(latent_path)

        if samples is None:
            samples = {'samples': torch.zeros([1, 4, 8, 8])}

        preview = self.parse_filename(latent_name)

        return {
                'ui': {"images": [preview]},
                'result': (samples, )
                }

    @classmethod
    def IS_CHANGED(s, latent, link_id, trigger_always):
        if trigger_always:
            return float("NaN")
        else:
            image_path = folder_paths.get_annotated_filepath(latent)
            m = hashlib.sha256()
            with open(image_path, 'rb') as f:
                m.update(f.read())
            return m.digest().hex()

    @classmethod
    def VALIDATE_INPUTS(s, latent, link_id, trigger_always):
        if not folder_paths.exists_annotated_filepath(latent) or latent.startswith("/") or ".." in latent:
            return "Invalid latent file: {}".format(latent)
        return True

```
