# HF Transformers Classifier Provider
## Documentation
- Class name: `ImpactHFTransformersClassifierProvider`
- Category: `ImpactPack/HuggingFace`
- Output node: `False`

This node provides a classifier based on Hugging Face Transformers models. It allows selecting a pre-defined model from a list or specifying a custom model repository ID. The classifier can be run on a preferred device mode, including automatic selection, GPU preference, or CPU only.
## Input types
### Required
- **`preset_repo_id`**
    - Specifies the repository ID of the pre-defined Hugging Face Transformers model to use. If 'Manual repo id' is selected, the manual_repo_id must be provided. This selection enables the use of a wide range of gender classification models or a custom model specified by the user.
    - Python dtype: `Union[List[str], str]`
    - Comfy dtype: `['STRING']`
- **`manual_repo_id`**
    - Allows specifying a custom repository ID for the Hugging Face Transformers model when 'Manual repo id' is selected in preset_repo_id. This enables the use of any model from the Hugging Face Hub not included in the pre-defined list.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`device_mode`**
    - Determines the device on which the classifier will run. Options include automatic device selection, preference for GPU, or CPU only, affecting the performance and efficiency of the model execution.
    - Python dtype: `List[str]`
    - Comfy dtype: `['STRING']`
## Output types
- **`transformers_classifier`**
    - Returns the initialized Hugging Face Transformers classifier ready for inference. This classifier can be used to classify images based on the selected or specified model.
    - Python dtype: `transformers.Pipeline`
    - Comfy dtype: `TRANSFORMERS_CLASSIFIER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class HF_TransformersClassifierProvider:
    @classmethod
    def INPUT_TYPES(s):
        global hf_transformer_model_urls
        return {"required": {
                        "preset_repo_id": (hf_transformer_model_urls + ['Manual repo id'],),
                        "manual_repo_id": ("STRING", {"multiline": False}),
                        "device_mode": (["AUTO", "Prefer GPU", "CPU"],),
                     },
                }

    RETURN_TYPES = ("TRANSFORMERS_CLASSIFIER",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/HuggingFace"

    def doit(self, preset_repo_id, manual_repo_id, device_mode):
        from transformers import pipeline

        if preset_repo_id == 'Manual repo id':
            url = manual_repo_id
        else:
            url = preset_repo_id

        if device_mode != 'CPU':
            device = comfy.model_management.get_torch_device()
        else:
            device = "cpu"

        classifier = pipeline(model=url, device=device)

        return (classifier,)

```
