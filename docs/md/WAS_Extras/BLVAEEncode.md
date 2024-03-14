# VAEEncode (Bundle Latent)
## Documentation
- Class name: `BLVAEEncode`
- Category: `latent`
- Output node: `False`

The BLVAEEncode node is designed for encoding images into a latent representation using a Variational Autoencoder (VAE). It supports both standard and tiled encoding modes, allowing for flexibility in handling images of various sizes. Additionally, it provides functionality for storing or loading the encoded latent representations, which can be useful in workflows that require persistent state or caching of intermediate results.
## Input types
### Required
- **`vae`**
    - Specifies the Variational Autoencoder (VAE) model to be used for encoding. This parameter is crucial as it determines the encoding mechanism and the quality of the generated latent representation.
    - Python dtype: `VAEModel`
    - Comfy dtype: `VAE`
- **`tiled`**
    - A boolean flag indicating whether tiled encoding should be used. Tiled encoding can be beneficial for large images, as it processes the image in smaller, manageable tiles.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`tile_size`**
    - Defines the size of the tiles used in tiled encoding. This parameter is relevant only when tiled encoding is enabled and affects the granularity of the encoding process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`store_or_load_latent`**
    - A boolean flag that controls whether the encoded latent representation should be stored or loaded. This functionality is useful for caching purposes or when working with precomputed latents.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`remove_latent_on_load`**
    - Determines whether the latent representation should be removed from storage after loading. This can help manage storage space when caching latents.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`delete_workflow_latent`**
    - A boolean flag that indicates whether the latent representation associated with a workflow should be deleted. This is useful for cleaning up resources and managing storage.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`image`**
    - The input image to be encoded into a latent representation. This parameter is optional and is only required if an image is to be encoded.
    - Python dtype: `ImageType`
    - Comfy dtype: `IMAGE`
## Output types
- **`latent`**
    - The encoded latent representation of the input image. This output is crucial for subsequent processing steps that require a latent representation, such as decoding or manipulation.
    - Python dtype: `dict`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The BLVAEEncode node is primarily utilized for transforming images into a compact latent representation using a Variational Autoencoder (VAE), suitable for both standard and tiled encoding to accommodate images of varying sizes. It is often used in scenarios requiring the storage or retrieval of these latent representations for efficient processing or caching in AI workflows.
## Source code
```python
class BLVAEEncode:
    def __init__(self):
        self.VAEEncode = nodes.VAEEncode()
        self.VAEEncodeTiled = nodes.VAEEncodeTiled()
        self.last_hash = None

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "vae": ("VAE",),
                "tiled": ("BOOLEAN", {"default": False}),
                "tile_size": ("INT", {"default": 512, "min": 320, "max": 4096, "step": 64}),
                "store_or_load_latent": ("BOOLEAN", {"default": True}),
                "remove_latent_on_load": ("BOOLEAN", {"default": True}),
                "delete_workflow_latent": ("BOOLEAN", {"default": False})
            },
            "optional": {
                "image": ("IMAGE",),
            },
            "hidden": {
                "extra_pnginfo": "EXTRA_PNGINFO",
                "unique_id": "UNIQUE_ID"
            }
        }

    RETURN_TYPES = ("LATENT", )
    RETURN_NAMES = ("latent", )
    
    FUNCTION = "encode"
    CATEGORY = "latent"

    def encode(self, vae, tiled, tile_size, store_or_load_latent, remove_latent_on_load, delete_workflow_latent, image=None, extra_pnginfo=None, unique_id=None):
        workflow_latent = None
        latent_key = f"latent_{unique_id}"

        if self.last_hash and torch.is_tensor(image):
            if self.last_hash is not self.sha256(image):
                delete_workflow_latent = True
        if torch.is_tensor(image):
            self.last_hash = self.sha256(image)

        if delete_workflow_latent:
            if extra_pnginfo['workflow']['extra'].__contains__(latent_key):
                try:
                    del extra_pnginfo['workflow']['extra'][latent_key]
                except Exception:
                    print(f"Unable to delete latent image from workflow node: {unqiue_id}")
                    pass

        if store_or_load_latent and unique_id:
            if latent_key in extra_pnginfo['workflow']['extra']:
                print(f"Loading latent image from workflow node: {unique_id}")
                try:
                    workflow_latent = self.deserialize(extra_pnginfo['workflow']['extra'][latent_key])
                except Exception as e:
                    print("There was an issue extracting the latent tensor from the workflow. Is it corrupted?")
                    workflow_latent = None
                    if not torch.is_tensor(image):
                        raise ValueError(f"Node {unique_id}: There was no image provided, and workflow latent missing. Unable to proceed.")
                
                if workflow_latent and remove_latent_on_load:
                    try:
                        del extra_pnginfo['workflow']['extra'][latent_key]
                    except Exception:
                        pass

        if workflow_latent:
            print(f"Loaded workflow latent from node: {unique_id}")
            return workflow_latent, { "extra_pnginfo": extra_pnginfo }

        if not torch.is_tensor(image):
            raise ValueError(f"Node {unique_id}: No workflow latent was loaded, and no image provided to encode. Unable to proceed. ")

        if tiled:
            encoded = self.VAEEncodeTiled.encode(pixels=image, tile_size=tile_size, vae=vae)
        else:
            encoded = self.VAEEncode.encode(pixels=image, vae=vae)

        if store_or_load_latent and unique_id:
            print(f"Saving latent to workflow node {unique_id}")
            new_workflow_latent = self.serialize(encoded[0])
            extra_pnginfo['workflow']['extra'][latent_key] = new_workflow_latent

        return encoded[0], { "extra_pnginfo": extra_pnginfo }

    def sha256(self, tensor):
        tensor_bytes = tensor.cpu().contiguous().numpy().tobytes()
        hash_obj = hashlib.sha256()
        hash_obj.update(tensor_bytes)
        return hash_obj.hexdigest()

    def serialize(self, obj):
        json_str = json.dumps(obj, default=lambda o: {'__tensor__': True, 'value': o.cpu().numpy().tolist()} if torch.is_tensor(o) else o.__dict__)
        compressed_data = zlib.compress(json_str.encode('utf-8'))
        base64_encoded = base64.b64encode(compressed_data).decode('utf-8')
        return base64_encoded

    def deserialize(self, base64_str):
        compressed_data = base64.b64decode(base64_str)
        json_str = zlib.decompress(compressed_data).decode('utf-8')
        obj = json.loads(json_str, object_hook=lambda d: torch.tensor(d['value']) if '__tensor__' in d else d)
        return obj

```
