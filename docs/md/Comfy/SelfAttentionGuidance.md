# Self-Attention Guidance
## Documentation
- Class name: `SelfAttentionGuidance`
- Category: `_for_testing`
- Output node: `False`

The `SelfAttentionGuidance` node likely encapsulates functionality related to guiding the attention mechanism in neural networks, specifically focusing on self-attention. This could involve modifying or enhancing the attention scores or outputs based on certain criteria or additional inputs to improve the model's performance or interpretability.
## Input types
### Required
- **`model`**
    - The model parameter represents the neural network model that will be guided or modified by the SelfAttentionGuidance functionality. It is crucial for defining the structure and behavior of the attention mechanism within the model.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`scale`**
    - The scale parameter likely adjusts the magnitude of guidance applied to the attention mechanism, influencing how strongly the guidance affects the model's attention scores or outputs.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`blur_sigma`**
    - The blur_sigma parameter probably controls the amount of blurring applied in the guidance process, potentially used to smooth or soften the guidance effects on the attention mechanism.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The modified neural network model with adjusted attention mechanism, reflecting the guidance applied through the SelfAttentionGuidance process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,Reroute,ADE_AnimateDiffLoaderWithContext,Attention couple,UltimateSDUpscale`


## Source code
```python
class SelfAttentionGuidance:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                             "scale": ("FLOAT", {"default": 0.5, "min": -2.0, "max": 5.0, "step": 0.1}),
                             "blur_sigma": ("FLOAT", {"default": 2.0, "min": 0.0, "max": 10.0, "step": 0.1}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "_for_testing"

    def patch(self, model, scale, blur_sigma):
        m = model.clone()

        attn_scores = None

        # TODO: make this work properly with chunked batches
        #       currently, we can only save the attn from one UNet call
        def attn_and_record(q, k, v, extra_options):
            nonlocal attn_scores
            # if uncond, save the attention scores
            heads = extra_options["n_heads"]
            cond_or_uncond = extra_options["cond_or_uncond"]
            b = q.shape[0] // len(cond_or_uncond)
            if 1 in cond_or_uncond:
                uncond_index = cond_or_uncond.index(1)
                # do the entire attention operation, but save the attention scores to attn_scores
                (out, sim) = attention_basic_with_sim(q, k, v, heads=heads)
                # when using a higher batch size, I BELIEVE the result batch dimension is [uc1, ... ucn, c1, ... cn]
                n_slices = heads * b
                attn_scores = sim[n_slices * uncond_index:n_slices * (uncond_index+1)]
                return out
            else:
                return optimized_attention(q, k, v, heads=heads)

        def post_cfg_function(args):
            nonlocal attn_scores
            uncond_attn = attn_scores

            sag_scale = scale
            sag_sigma = blur_sigma
            sag_threshold = 1.0
            model = args["model"]
            uncond_pred = args["uncond_denoised"]
            uncond = args["uncond"]
            cfg_result = args["denoised"]
            sigma = args["sigma"]
            model_options = args["model_options"]
            x = args["input"]
            if min(cfg_result.shape[2:]) <= 4: #skip when too small to add padding
                return cfg_result

            # create the adversarially blurred image
            degraded = create_blur_map(uncond_pred, uncond_attn, sag_sigma, sag_threshold)
            degraded_noised = degraded + x - uncond_pred
            # call into the UNet
            (sag, _) = comfy.samplers.calc_cond_uncond_batch(model, uncond, None, degraded_noised, sigma, model_options)
            return cfg_result + (degraded - sag) * sag_scale

        m.set_model_sampler_post_cfg_function(post_cfg_function, disable_cfg1_optimization=True)

        # from diffusers:
        # unet.mid_block.attentions[0].transformer_blocks[0].attn1.patch
        m.set_model_attn1_replace(attn_and_record, "middle", 0, 0)

        return (m, )

```
