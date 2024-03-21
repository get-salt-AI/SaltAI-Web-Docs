# Perp-Neg
## Documentation
- Class name: `PerpNeg`
- Category: `_for_testing`
- Output node: `False`

The PerpNeg node applies a specialized transformation to a given model, focusing on adjusting the model's behavior based on negative conditioning. It clones the input model and modifies its sampling configuration function to compute a perpendicular negative (Perp-Neg) vector, which is scaled and used to adjust the model's output. This process involves complex tensor operations and is designed to enhance the model's generation capabilities by incorporating negative conditioning in a novel way.
## Input types
### Required
- **`model`**
    - The input model to be transformed. This model is cloned and its sampling configuration is modified to incorporate the perpendicular negative vector adjustment.
    - Python dtype: `comfy.model_management.Model`
    - Comfy dtype: `MODEL`
- **`empty_conditioning`**
    - An empty conditioning input that serves as a baseline for calculating the perpendicular negative vector. It's used to encode and process model conditions without any positive conditioning.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `CONDITIONING`
- **`neg_scale`**
    - A scaling factor for the perpendicular negative vector. This scale adjusts the influence of the negative conditioning on the model's output, allowing for fine-tuning of the generation process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The transformed model with a modified sampling configuration to incorporate the perpendicular negative vector adjustment. This model is expected to have enhanced generation capabilities by effectively utilizing negative conditioning.
    - Python dtype: `comfy.model_management.Model`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class PerpNeg:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"model": ("MODEL", ),
                             "empty_conditioning": ("CONDITIONING", ),
                             "neg_scale": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0}),
                            }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "_for_testing"

    def patch(self, model, empty_conditioning, neg_scale):
        m = model.clone()
        nocond = comfy.sample.convert_cond(empty_conditioning)

        def cfg_function(args):
            model = args["model"]
            noise_pred_pos = args["cond_denoised"]
            noise_pred_neg = args["uncond_denoised"]
            cond_scale = args["cond_scale"]
            x = args["input"]
            sigma = args["sigma"]
            model_options = args["model_options"]
            nocond_processed = comfy.samplers.encode_model_conds(model.extra_conds, nocond, x, x.device, "negative")

            (noise_pred_nocond, _) = comfy.samplers.calc_cond_uncond_batch(model, nocond_processed, None, x, sigma, model_options)

            pos = noise_pred_pos - noise_pred_nocond
            neg = noise_pred_neg - noise_pred_nocond
            perp = neg - ((torch.mul(neg, pos).sum())/(torch.norm(pos)**2)) * pos
            perp_neg = perp * neg_scale
            cfg_result = noise_pred_nocond + cond_scale*(pos - perp_neg)
            cfg_result = x - cfg_result
            return cfg_result

        m.set_model_sampler_cfg_function(cfg_function)

        return (m, )

```
