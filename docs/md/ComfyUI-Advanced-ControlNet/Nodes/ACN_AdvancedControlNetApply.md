---
tags:
- Conditioning
- ControlNet
- ControlNetLoader
---

# Apply Advanced ControlNet 🛂🅐🅒🅝
## Documentation
- Class name: `ACN_AdvancedControlNetApply`
- Category: `Adv-ControlNet 🛂🅐🅒🅝`
- Output node: `False`

The ACN_AdvancedControlNetApply node is designed to apply advanced control mechanisms to images, utilizing a control network to modify or enhance image features based on conditioning inputs. It extends the capabilities of standard control networks by incorporating additional parameters and techniques for more nuanced and precise image manipulation.
## Input types
### Required
- **`positive`**
    - Specifies the positive conditioning to guide the control network towards desired image attributes, playing a crucial role in the image manipulation process.
    - Comfy dtype: `CONDITIONING`
    - Python dtype: `tuple`
- **`negative`**
    - Defines the negative conditioning to steer the control network away from undesired image attributes, contributing to the refinement of the manipulation outcome.
    - Comfy dtype: `CONDITIONING`
    - Python dtype: `tuple`
- **`control_net`**
    - The control network model used for applying the conditioning to the image, central to the node's functionality.
    - Comfy dtype: `CONTROL_NET`
    - Python dtype: `ControlNet`
- **`image`**
    - The input image to be manipulated by the control network.
    - Comfy dtype: `IMAGE`
    - Python dtype: `torch.Tensor`
- **`strength`**
    - Controls the intensity of the control network's effect on the image, allowing for subtle to strong modifications.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`start_percent`**
    - Determines the starting point of the control effect in terms of the image's generation process, enabling targeted manipulation.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`end_percent`**
    - Specifies the endpoint of the control effect, allowing for precise control over the extent of manipulation.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
### Optional
- **`mask_optional`**
    - An optional mask to specify areas of the image to be affected by the control network, enhancing the precision of manipulation.
    - Comfy dtype: `MASK`
    - Python dtype: `torch.Tensor`
- **`timestep_kf`**
    - Optional keyframe for controlling the application of the control network over time, adding temporal dynamics to the manipulation.
    - Comfy dtype: `TIMESTEP_KEYFRAME`
    - Python dtype: `dict`
- **`latent_kf_override`**
    - Optional override for latent keyframes, allowing for advanced manipulation of the control network's behavior over time.
    - Comfy dtype: `LATENT_KEYFRAME`
    - Python dtype: `dict`
- **`weights_override`**
    - Optional parameter to override the default weights of the control network, enabling customized control effects.
    - Comfy dtype: `CONTROL_NET_WEIGHTS`
    - Python dtype: `dict`
- **`model_optional`**
    - An optional model parameter that can be used in conjunction with the control network for enhanced manipulation capabilities.
    - Comfy dtype: `MODEL`
    - Python dtype: `Model`
- **`vae_optional`**
    - An optional variational autoencoder model used in conjunction with the control network to enhance the manipulation process.
    - Comfy dtype: `VAE`
    - Python dtype: `VAE`
## Output types
- **`positive`**
    - Comfy dtype: `CONDITIONING`
    - The modified positive conditioning after applying the control network, reflecting the changes made to the image.
    - Python dtype: `tuple`
- **`negative`**
    - Comfy dtype: `CONDITIONING`
    - The modified negative conditioning after applying the control network, reflecting the changes made to the image.
    - Python dtype: `tuple`
- **`model_opt`**
    - Comfy dtype: `MODEL`
    - An optional model output, potentially modified by the control network application.
    - Python dtype: `Model`
## Usage tips
- Infra type: `GPU`
- Common nodes:
    - [KSampler](../../Comfy/Nodes/KSampler.md)
    - [SamplerCustom](../../Comfy/Nodes/SamplerCustom.md)
    - [ACN_AdvancedControlNetApply](../../ComfyUI-Advanced-ControlNet/Nodes/ACN_AdvancedControlNetApply.md)
    - [ControlNetApplyAdvanced](../../Comfy/Nodes/ControlNetApplyAdvanced.md)
    - [KSampler (Efficient)](../../efficiency-nodes-comfyui/Nodes/KSampler (Efficient).md)
    - [ConditioningSetMask](../../Comfy/Nodes/ConditioningSetMask.md)



## Source code
```python
class AdvancedControlNetApply:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "positive": ("CONDITIONING", ),
                "negative": ("CONDITIONING", ),
                "control_net": ("CONTROL_NET", ),
                "image": ("IMAGE", ),
                "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                "end_percent": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001})
            },
            "optional": {
                "mask_optional": ("MASK", ),
                "timestep_kf": ("TIMESTEP_KEYFRAME", ),
                "latent_kf_override": ("LATENT_KEYFRAME", ),
                "weights_override": ("CONTROL_NET_WEIGHTS", ),
                "model_optional": ("MODEL",),
                "vae_optional": ("VAE",),
            }
        }

    RETURN_TYPES = ("CONDITIONING","CONDITIONING","MODEL",)
    RETURN_NAMES = ("positive", "negative", "model_opt")
    FUNCTION = "apply_controlnet"

    CATEGORY = "Adv-ControlNet 🛂🅐🅒🅝"

    def apply_controlnet(self, positive, negative, control_net, image, strength, start_percent, end_percent,
                         mask_optional: Tensor=None, model_optional: ModelPatcher=None, vae_optional=None,
                         timestep_kf: TimestepKeyframeGroup=None, latent_kf_override: LatentKeyframeGroup=None,
                         weights_override: ControlWeights=None):
        if strength == 0:
            return (positive, negative, model_optional)
        if model_optional:
            model_optional = model_optional.clone()

        control_hint = image.movedim(-1,1)
        cnets = {}

        out = []
        for conditioning in [positive, negative]:
            c = []
            for t in conditioning:
                d = t[1].copy()

                prev_cnet = d.get('control', None)
                if prev_cnet in cnets:
                    c_net = cnets[prev_cnet]
                else:
                    # copy, convert to advanced if needed, and set cond
                    c_net = convert_to_advanced(control_net.copy()).set_cond_hint(control_hint, strength, (start_percent, end_percent), vae_optional)
                    if is_advanced_controlnet(c_net):
                        # disarm node check
                        c_net.disarm()
                        # if model required, verify model is passed in, and if so patch it
                        if c_net.require_model:
                            if not model_optional:
                                raise Exception(f"Type '{type(c_net).__name__}' requires model_optional input, but got None.")
                            c_net.patch_model(model=model_optional)
                        # if vae required, verify vae is passed in
                        if c_net.require_vae:
                            # if controlnet can accept preprocced condhint latents and is the case, ignore vae requirement
                            if c_net.allow_condhint_latents and isinstance(control_hint, AbstractPreprocWrapper):
                                pass
                            elif not vae_optional:
                                # make sure SD3 ControlNet will get a special message instead of generic type mention
                                if is_sd3_advanced_controlnet:
                                    raise Exception(f"SD3 ControlNet requires vae_optional input, but got None.")
                                else:
                                    raise Exception(f"Type '{type(c_net).__name__}' requires vae_optional input, but got None.")
                        # apply optional parameters and overrides, if provided
                        if timestep_kf is not None:
                            c_net.set_timestep_keyframes(timestep_kf)
                        if latent_kf_override is not None:
                            c_net.latent_keyframe_override = latent_kf_override
                        if weights_override is not None:
                            c_net.weights_override = weights_override
                        # verify weights are compatible
                        c_net.verify_all_weights()
                        # set cond hint mask
                        if mask_optional is not None:
                            mask_optional = mask_optional.clone()
                            # if not in the form of a batch, make it so
                            if len(mask_optional.shape) < 3:
                                mask_optional = mask_optional.unsqueeze(0)
                            c_net.set_cond_hint_mask(mask_optional)
                    c_net.set_previous_controlnet(prev_cnet)
                    cnets[prev_cnet] = c_net

                d['control'] = c_net
                d['control_apply_to_uncond'] = False
                n = [t[0], d]
                c.append(n)
            out.append(c)
        return (out[0], out[1], model_optional)

```
