# FreeInit Iteration Options 🎭🅐🅓
## Documentation
- Class name: `ADE_IterationOptsFreeInit`
- Category: `Animate Diff 🎭🅐🅓/iteration opts`
- Output node: `False`

The `ADE_IterationOptsFreeInit` node is designed to create iteration options for the Animate Diff process, allowing for customization of iteration parameters such as the number of iterations, batch and seed offsets. This node plays a crucial role in configuring the iteration behavior for animation generation, enabling fine-tuning of the animation process.
## Input types
### Required
- **`iterations`**
    - Specifies the number of iterations to perform. This parameter is fundamental in determining the length and detail of the animation process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`filter`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['gaussian', 'butterworth', 'ideal'...]`
- **`d_s`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`d_t`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `FLOAT`
- **`n_butterworth`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`sigma_step`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
- **`apply_to_1st_iter`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
- **`init_type`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `['FreeInit [sampler sigma]', 'FreeInit [model sigma]', 'DinkInit_v1'...]`
### Optional
- **`iter_batch_offset`**
    - Determines the offset for batch processing, allowing for adjustments in the processing sequence of batches. This can affect the distribution of computational resources.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`iter_seed_offset`**
    - Sets the seed offset, which influences the randomness of the iteration process. This can be used to achieve different random effects or to replicate specific results.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`iteration_opts`**
    - The iteration options configured based on the input parameters. These options are used to control the iteration process in the Animate Diff animation generation.
    - Python dtype: `IterationOptions`
    - Comfy dtype: `ITERATION_OPTS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class FreeInitOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "iterations": ("INT", {"default": 2, "min": 1}),
                "filter": (FreeInitFilter.LIST,),
                "d_s": ("FLOAT", {"default": 0.25, "min": 0.0, "max": 1.0, "step": 0.001}),
                "d_t": ("FLOAT", {"default": 0.25, "min": 0.0, "max": 1.0, "step": 0.001}),
                "n_butterworth": ("INT", {"default": 4, "min": 1, "max": 100},),
                "sigma_step": ("INT", {"default": 999, "min": 1, "max": 999}),
                "apply_to_1st_iter": ("BOOLEAN", {"default": False}),
                "init_type": (FreeInitOptions.LIST,)
            },
            "optional": {
                "iter_batch_offset": ("INT", {"default": 0, "min": 0, "max": BIGMAX}),
                "iter_seed_offset": ("INT", {"default": 1, "min": BIGMIN, "max": BIGMAX}),
            }
        }
    
    RETURN_TYPES = ("ITERATION_OPTS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/iteration opts"
    FUNCTION = "create_iter_opts"

    def create_iter_opts(self, iterations: int, filter: str, d_s: float, d_t: float, n_butterworth: int,
                         sigma_step: int, apply_to_1st_iter: bool, init_type: str,
                         iter_batch_offset: int=0, iter_seed_offset: int=1):
        # init_type does nothing for now, not until I add more methods of applying low+high freq noise
        iter_opts = FreeInitOptions(iterations=iterations, step=sigma_step, apply_to_1st_iter=apply_to_1st_iter,
                                    filter=filter, d_s=d_s, d_t=d_t, n=n_butterworth, init_type=init_type,
                                    iter_batch_offset=iter_batch_offset, iter_seed_offset=iter_seed_offset)
        return (iter_opts,)

```
