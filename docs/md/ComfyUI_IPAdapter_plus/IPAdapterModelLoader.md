# Load IPAdapter Model
## Documentation
- Class name: `IPAdapterModelLoader`
- Category: `ipadapter`
- Output node: `False`

This node is responsible for loading an IPAdapter model from a specified file. It supports loading models saved in a custom format, including handling specific model components like 'image_proj' and 'ip_adapter'. If the model file is in a '.safetensors' format, it restructures the model accordingly. The node ensures the loaded model contains the 'ip_adapter' component, throwing an exception if it's missing.
## Input types
### Required
- **`ipadapter_file`**
    - Specifies the filename of the IPAdapter model to be loaded. This parameter is crucial as it determines which model file is accessed and loaded.
    - Python dtype: `List[str]`
    - Comfy dtype: `MODEL_PATHS`
## Output types
- **`ipadapter`**
    - The loaded IPAdapter model. It's either directly loaded or restructured if in '.safetensors' format, ready for further processing or application.
    - Python dtype: `Dict[str, Any]`
    - Comfy dtype: `IPADAPTER`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IPAdapterApply,IPAdapterApplyFaceID,IPAdapterApplyEncoded,ToIPAdapterPipe //Inspire,Reroute,AV_IPAdapter`

The IPAdapterModelLoader node is responsible for loading IPAdapter models, which are specialized for adapting and enhancing images within the pipeline. It takes a model file name as input and outputs the loaded IPAdapter model (`IPADAPTER`), ready for further processing or application by subsequent nodes.
## Source code
```python
class IPAdapterModelLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "ipadapter_file": (folder_paths.get_filename_list("ipadapter"), )}}

    RETURN_TYPES = ("IPADAPTER",)
    FUNCTION = "load_ipadapter_model"
    CATEGORY = "ipadapter"

    def load_ipadapter_model(self, ipadapter_file):
        ckpt_path = folder_paths.get_full_path("ipadapter", ipadapter_file)

        model = comfy.utils.load_torch_file(ckpt_path, safe_load=True)

        if ckpt_path.lower().endswith(".safetensors"):
            st_model = {"image_proj": {}, "ip_adapter": {}}
            for key in model.keys():
                if key.startswith("image_proj."):
                    st_model["image_proj"][key.replace("image_proj.", "")] = model[key]
                elif key.startswith("ip_adapter."):
                    st_model["ip_adapter"][key.replace("ip_adapter.", "")] = model[key]
            model = st_model
                    
        if not "ip_adapter" in model.keys() or not model["ip_adapter"]:
            raise Exception("invalid IPAdapter model {}".format(ckpt_path))

        return (model,)

```
