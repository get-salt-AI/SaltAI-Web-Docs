# KSampler Sequence
## Documentation
- Class name: `KSamplerSeq`
- Category: `sampling`
- Output node: `False`

The `KSamplerSeq` node is designed for advanced image sampling in sequences, incorporating features like latent interpolation, conditioning slerp, and optional unsampling of latents. It allows for dynamic adjustment of denoising levels and supports various seed modes and alternate value toggling for enhanced control over the sampling process.
## Input types
### Required
- **`model`**
    - Specifies the model to be used for sampling, central to the operation of the node.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`seed`**
    - Determines the initial seed for randomness, affecting the sampling outcome. It can be dynamically adjusted based on the seed mode.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`seed_mode_seq`**
    - Controls how the seed is updated across sequence iterations, allowing for incremental, decremental, random, or fixed seed values.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`alternate_values`**
    - Enables or disables the alternation of certain parameters between sequence iterations, adding variability to the sampling process.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`steps`**
    - Defines the number of steps to be used in the sampling process, impacting the detail and quality of the generated images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - Sets the classifier-free guidance scale, influencing the adherence to the provided conditioning.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - Selects the specific sampling algorithm to be used, affecting the characteristics of the generated images.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Chooses the scheduler for controlling the sampling process, further customizing the generation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`sequence_loop_count`**
    - Determines the number of iterations for the sequence, allowing for extended or shortened sampling sequences.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`positive_seq`**
    - Provides the positive conditioning sequence, guiding the sampling towards desired attributes.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `CONDITIONING_SEQ`
- **`negative_seq`**
    - Supplies the negative conditioning sequence, steering the sampling away from undesired attributes.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `CONDITIONING_SEQ`
- **`use_conditioning_slerp`**
    - Enables spherical linear interpolation (slerp) for conditioning, blending positive and negative conditioning smoothly.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`cond_slerp_strength`**
    - Adjusts the strength of the conditioning slerp, controlling the blend between positive and negative conditioning.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`latent_image`**
    - Provides the initial latent image for the sampling process, serving as a starting point.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`use_latent_interpolation`**
    - Activates latent interpolation, allowing for smooth transitions between latent images.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`latent_interpolation_mode`**
    - Determines the method of latent interpolation, offering options like blend, slerp, and cosine interpolation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`latent_interp_strength`**
    - Controls the intensity of the latent interpolation, affecting the smoothness of transitions.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`denoise_start`**
    - Sets the initial denoising level, impacting the clarity and detail of the initial images in the sequence.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`denoise_seq`**
    - Specifies the denoising level for subsequent images in the sequence, allowing for dynamic adjustment of image clarity.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`unsample_latents`**
    - Determines whether to perform unsampling on the latents, potentially enhancing image quality at the cost of additional computation.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`latent`**
    - Produces a sequence of sampled images based on the provided parameters and conditions, showcasing the node's capability to generate diverse and high-quality images.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The `KSamplerSeq` node is pivotal for advanced image sequence generation, enabling features like latent interpolation and conditioning slerp, and is often used with conditioning sequences to guide the sampling towards or away from specific attributes. It takes a model, seed, and various sampling parameters as input, and outputs a sequence of sampled images, allowing for dynamic adjustments in denoising levels and seed modes for enhanced control over the image generation process.
## Source code
```python
class KSamplerSeq:

    def __init__(self):
        self.previous_seed = None
        self.current_seed = None

    def initialize_seeds(self, initial_seed):
        self.previous_seed = initial_seed
        self.current_seed = initial_seed

    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"model": ("MODEL",),
                    "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "seed_mode_seq": (["increment", "decrement", "random", "fixed"],),
                    "alternate_values": ("BOOLEAN", {"default": True}),
                    "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                    "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0, "step":0.5, "round": 0.01}),
                    "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                    "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                    "sequence_loop_count": ("INT", {"default": 20, "min": 1, "max": 1024, "step": 1}),
                    "positive_seq": ("CONDITIONING_SEQ", ),
                    "negative_seq": ("CONDITIONING_SEQ", ),
                    "use_conditioning_slerp": ("BOOLEAN", {"default": False}),
                    "cond_slerp_strength": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.001}),
                    "latent_image": ("LATENT", ),
                    "use_latent_interpolation": ("BOOLEAN", {"default": False}),
                    "latent_interpolation_mode": (["Blend", "Slerp", "Cosine Interp"],),
                    "latent_interp_strength": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.001}),
                    "denoise_start": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                    "denoise_seq": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                    "unsample_latents": ("BOOLEAN", {"default": False})
                     }
                }

    RETURN_TYPES = ("LATENT",)
    FUNCTION = "sample"

    CATEGORY = "sampling"

    def update_seed(self, seed, seed_mode):
        if seed_mode == "increment":
            return seed + 1
        elif seed_mode == "decrement":
            return seed - 1
        elif seed_mode == "random":
            return random.randint(0, 0xffffffffffffffff)
        elif seed_mode == "fixed":
            return seed

    def hash_tensor(self, tensor):
        tensor = tensor.cpu().contiguous()
        return hashlib.sha256(tensor.numpy().tobytes()).hexdigest()

    def update_conditioning(self, conditioning_seq, loop_count, last_conditioning):
        matching_conditioning = None
        for idx, conditioning, *_ in conditioning_seq:
            if int(idx) == loop_count:
                matching_conditioning = conditioning
                break
        return matching_conditioning if matching_conditioning else (last_conditioning if last_conditioning else None)

    def update_alternate_seed(self, loop_count):
        if loop_count % 3 == 0:
            if self.previous_seed is None:
                self.previous_seed = self.current_seed
            else:
                self.previous_seed, self.current_seed = self.current_seed, self.previous_seed + 1 if loop_count // 2 % 2 == 0 else self.previous_seed - 1
        return self.current_seed

    def alternate_denoise(self, current_denoise):
        return 0.95 if current_denoise == 0.75 else 0.75

    def sample(self, model, seed, seed_mode_seq, alternate_values, steps, cfg, sampler_name, scheduler, sequence_loop_count, positive_seq, negative_seq, cond_slerp_strength, latent_image, use_latent_interpolation, latent_interpolation_mode, latent_interp_strength, denoise_start=1.0, denoise_seq=0.5, use_conditioning_slerp=False, unsample_latents=False, alternate_mode=False):
        positive_seq = positive_seq
        negative_seq = negative_seq
        results = []
        positive_conditioning = None
        negative_conditioning = None

        self.initialize_seeds(seed)

        for loop_count in range(sequence_loop_count):
            if alternate_values and loop_count % 2 == 0:
                seq_seed = self.update_alternate_seed(seed) if seed_mode_seq != "fixed" else seed
                #denoise_seq = self.alternate_denoise(denoise_seq)
            else:
                seq_seed = seed if loop_count <= 0 else self.update_seed(seq_seed, seed_mode_seq)


            print(f"Loop count: {loop_count}, Seed: {seq_seed}")

            last_positive_conditioning = positive_conditioning[0] if positive_conditioning else None
            last_negative_conditioning = negative_conditioning[0] if negative_conditioning else None

            positive_conditioning = self.update_conditioning(positive_seq, loop_count, last_positive_conditioning)
            negative_conditioning = self.update_conditioning(negative_seq, loop_count, last_negative_conditioning)

            if use_conditioning_slerp and (last_positive_conditioning and last_negative_conditioning):
                a = last_positive_conditioning[0].clone()
                b = positive_conditioning[0].clone()
                na = last_negative_conditioning[0].clone()
                nb = negative_conditioning[0].clone()

                pa = last_positive_conditioning[1]["pooled_output"].clone()
                pb = positive_conditioning[1]["pooled_output"].clone()
                npa = last_negative_conditioning[1]["pooled_output"].clone()
                npb = negative_conditioning[1]["pooled_output"].clone()

                pos_cond = slerp(cond_slerp_strength, a, b)
                pos_pooled = slerp(cond_slerp_strength, pa, pb)
                neg_cond = slerp(cond_slerp_strength, na, nb)
                neg_pooled = slerp(cond_slerp_strength, npa, npb)
                
                positive_conditioning = [pos_cond, {"pooled_output": pos_pooled}]
                negative_conditioning = [neg_cond, {"pooled_output": neg_pooled}]

            positive_conditioning = [positive_conditioning]
            negative_conditioning = [negative_conditioning]

            if positive_conditioning is not None or negative_conditioning is not None:

                end_at_step = steps
                if results is not None and len(results) > 0:
                    latent_input = {'samples': results[-1]}
                    denoise = denoise_seq
                    start_at_step = round((1 - denoise) * steps)
                    end_at_step = steps
                else:
                    latent_input = latent_image
                    denoise = denoise_start

                if unsample_latents and loop_count > 0:
                    force_full_denoise = False if loop_count > 0 or loop_count <= steps - 1 else True
                    disable_noise = False
                    unsampled_latent = unsample(model=model, seed=seq_seed, cfg=cfg, sampler_name=sampler_name, steps=steps, end_at_step=end_at_step, scheduler=scheduler, normalize=False, positive=positive_conditioning, negative=negative_conditioning, latent_image=latent_input)[0]
                    sample = nodes.common_ksampler(model, seq_seed, steps, cfg, sampler_name, scheduler, positive_conditioning, negative_conditioning, unsampled_latent, denoise=denoise, disable_noise=disable_noise, start_step=start_at_step, last_step=end_at_step, force_full_denoise=force_full_denoise)[0]['samples']
                else:
                    sample = nodes.common_ksampler(model, seq_seed, steps, cfg, sampler_name, scheduler, positive_conditioning, negative_conditioning, latent_input, denoise=denoise)[0]['samples']

                if use_latent_interpolation and results and loop_count > 0:
                    if latent_interpolation_mode == "Blend":
                        sample = blend_latents(latent_interp_strength, results[-1], sample)
                    elif latent_interpolation_mode == "Slerp":
                        sample = slerp_latents(latent_interp_strength, results[-1], sample)
                    elif latent_interpolation_mode == "Cosine Interp":
                        sample = cosine_interp_latents(latent_interp_strength, results[-1], sample)
                    else:
                        sample = sample

                results.append(sample)

        results = torch.cat(results, dim=0)
        results = {'samples': results}

        return (results,)

```
