# LatentBatchSeedBehavior
## Documentation
- Class name: `LatentBatchSeedBehavior`
- Category: `latent/advanced`
- Output node: `False`

This node modifies the batch index of a given set of latent samples based on the specified seed behavior. It supports either a fixed or random seed behavior, which determines how the batch index is adjusted or removed to ensure consistency or variability in batch processing.
## Input types
### Required
- **`samples`**
    - The latent samples to be processed. This input is crucial for determining the structure and content of the output based on the seed behavior.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`seed_behavior`**
    - Determines whether the batch index should be adjusted in a fixed manner or removed for randomness. This choice affects how the samples are grouped or individualized during processing.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`latent`**
    - The modified set of latent samples, with adjustments to the batch index based on the seed behavior.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LatentBatchSeedBehavior:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",),
                              "seed_behavior": (["random", "fixed"],{"default": "fixed"}),}}

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "op"

    CATEGORY = "latent/advanced"

    def op(self, samples, seed_behavior):
        samples_out = samples.copy()
        latent = samples["samples"]
        if seed_behavior == "random":
            if 'batch_index' in samples_out:
                samples_out.pop('batch_index')
        elif seed_behavior == "fixed":
            batch_number = samples_out.get("batch_index", [0])[0]
            samples_out["batch_index"] = [batch_number] * latent.shape[0]

        return (samples_out,)

```
