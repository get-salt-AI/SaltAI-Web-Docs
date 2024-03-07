# ReferenceOnlySimple
## Documentation
- Class name: `ReferenceOnlySimple`
- Category: `custom_node_experiments`
- Output node: `False`

This node performs a reference-only operation on a given model by cloning the model and manipulating its attention mechanism based on a reference input. It generates a new latent representation by combining the reference with a batch of zeros, adjusts the attention mechanism to apply specific transformations, and handles optional noise masking for the output. The result is a modified model and a new set of latent samples, potentially with a noise mask, ready for further processing or generation tasks.
## Input types
- **`model`**
    - The model to be cloned and modified. This is the primary input for the node, as the operation revolves around altering the model's internal attention mechanism based on the reference input.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`reference`**
    - The reference input used to guide the modification of the model's attention mechanism. It provides the basis for generating the new latent representation.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`batch_size`**
    - Determines the size of the batch for the new latent representation. It affects the dimensions of the generated zeros tensor that is combined with the reference input.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`model`**
    - The cloned and modified model with an adjusted attention mechanism.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`latent`**
    - A dictionary containing the new set of latent samples and optionally a noise mask, ready for further processing.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ADE_AnimateDiffLoaderWithContext,LatentFromBatch`

The ReferenceOnlySimple node is primarily utilized for modifying a given model's attention mechanism by using a reference input to generate a new latent representation, often used with nodes that require a modified model for further processing or generation tasks. It takes a model and a reference latent input, along with an optional batch size, to produce a cloned model with adjusted attention and a new set of latent samples, potentially including a noise mask.
## Source code
```python
class ReferenceOnlySimple:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "reference": ("LATENT",),
                              "batch_size": ("INT", {"default": 1, "min": 1, "max": 64})
                              }}

    RETURN_TYPES = ("MODEL", "LATENT")
    FUNCTION = "reference_only"

    CATEGORY = "custom_node_experiments"

    def reference_only(self, model, reference, batch_size):
        model_reference = model.clone()
        size_latent = list(reference["samples"].shape)
        size_latent[0] = batch_size
        latent = {}
        latent["samples"] = torch.zeros(size_latent)

        batch = latent["samples"].shape[0] + reference["samples"].shape[0]
        def reference_apply(q, k, v, extra_options):
            k = k.clone().repeat(1, 2, 1)
            offset = 0
            if q.shape[0] > batch:
                offset = batch

            for o in range(0, q.shape[0], batch):
                for x in range(1, batch):
                    k[x + o, q.shape[1]:] = q[o,:]

            return q, k, k

        model_reference.set_model_attn1_patch(reference_apply)
        out_latent = torch.cat((reference["samples"], latent["samples"]))
        if "noise_mask" in latent:
            mask = latent["noise_mask"]
        else:
            mask = torch.ones((64,64), dtype=torch.float32, device="cpu")

        if len(mask.shape) < 3:
            mask = mask.unsqueeze(0)
        if mask.shape[0] < latent["samples"].shape[0]:
            print(latent["samples"].shape, mask.shape)
            mask = mask.repeat(latent["samples"].shape[0], 1, 1)

        out_mask = torch.zeros((1,mask.shape[1],mask.shape[2]), dtype=torch.float32, device="cpu")
        return (model_reference, {"samples": out_latent, "noise_mask": torch.cat((out_mask, mask))})

```
