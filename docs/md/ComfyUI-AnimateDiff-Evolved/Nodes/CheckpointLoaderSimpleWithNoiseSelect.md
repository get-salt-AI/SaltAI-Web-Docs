# Load Checkpoint w/ Noise Select 🎭🅐🅓
## Documentation
- Class name: `CheckpointLoaderSimpleWithNoiseSelect`
- Category: `Animate Diff 🎭🅐🅓/extras`
- Output node: `False`

This node is designed to load a model checkpoint with the option to select a specific noise configuration. It allows for the customization of the beta schedule used during the loading process and offers the flexibility to adjust the scale factor of the model's latent space if desired. This node is particularly useful for applications requiring precise control over the model initialization process, such as in animation or differential image generation tasks.
## Input types
### Required
- **`ckpt_name`**
    - Specifies the name of the checkpoint to be loaded. This parameter is crucial for identifying the specific model checkpoint file from a list of available checkpoints.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`beta_schedule`**
    - Determines the beta schedule to be applied to the model during the loading process. This affects the model's sampling behavior and can be customized to achieve different effects.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`use_custom_scale_factor`**
    - A flag indicating whether to use a custom scale factor for the model's latent space. Enabling this allows for fine-tuning of the model's behavior.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`scale_factor`**
    - The scale factor to be applied to the model's latent space when 'use_custom_scale_factor' is true. This allows for precise control over the model's output.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The loaded model, ready for further processing or generation tasks.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The CLIP model associated with the loaded checkpoint, if available.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
- **`vae`**
    - The VAE model associated with the loaded checkpoint, if available.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `LoraLoader,CLIPTextEncode,ADE_AnimateDiffLoaderWithContext,BatchPromptSchedule,CLIPSetLastLayer,Lora Loader Stack (rgthree),IPAdapterApply,ToBasicPipe`


## Source code
```python
class CheckpointLoaderSimpleWithNoiseSelect:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "ckpt_name": (folder_paths.get_filename_list("checkpoints"), ),
                "beta_schedule": (BetaSchedules.ALIAS_LIST, {"default": BetaSchedules.USE_EXISTING}, )
            },
            "optional": {
                "use_custom_scale_factor": ("BOOLEAN", {"default": False}),
                "scale_factor": ("FLOAT", {"default": 0.18215, "min": 0.0, "max": 1.0, "step": 0.00001})
            }
        }
    RETURN_TYPES = ("MODEL", "CLIP", "VAE")
    FUNCTION = "load_checkpoint"

    CATEGORY = "Animate Diff 🎭🅐🅓/extras"

    def load_checkpoint(self, ckpt_name, beta_schedule, output_vae=True, output_clip=True, use_custom_scale_factor=False, scale_factor=0.18215):
        ckpt_path = folder_paths.get_full_path("checkpoints", ckpt_name)
        out = load_checkpoint_guess_config(ckpt_path, output_vae=True, output_clip=True, embedding_directory=folder_paths.get_folder_paths("embeddings"))
        # register chosen beta schedule on model - convert to beta_schedule name recognized by ComfyUI
        new_model_sampling = BetaSchedules.to_model_sampling(beta_schedule, out[0])
        if new_model_sampling is not None:
            out[0].model.model_sampling = new_model_sampling
        if use_custom_scale_factor:
            out[0].model.latent_format.scale_factor = scale_factor
        return out

```
