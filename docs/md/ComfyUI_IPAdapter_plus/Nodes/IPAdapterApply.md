# Apply IPAdapter
## Documentation
- Class name: `IPAdapterApply`
- Category: `ipadapter`
- Output node: `False`

This node applies an IPAdapter to modify the embeddings or features of a model based on the provided inputs. It supports different weight types to adjust the influence of the IPAdapter, and can handle optional inputs like attention masks for more specialized adjustments.
## Input types
### Required
- **`ipadapter`**
    - The IPAdapter to be applied. It is crucial for modifying the embeddings or features of the model.
    - Python dtype: `IPAdapter`
    - Comfy dtype: `IPADAPTER`
- **`clip_vision`**
    - Optional. If provided, it is used to enhance the IPAdapter's application with vision-related features.
    - Python dtype: `ClipVisionModel`
    - Comfy dtype: `CLIP_VISION`
- **`image`**
    - Optional. An image that can be used in conjunction with the IPAdapter for vision-related tasks.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`model`**
    - The model whose embeddings or features are to be modified by the IPAdapter.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`weight`**
    - A scalar weight that adjusts the influence of the IPAdapter on the model's embeddings or features.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`noise`**
    - Optional. Noise level to be applied during the IPAdapter's application, providing a way to introduce variability.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`weight_type`**
    - Specifies the method to apply the weight, offering different strategies for adjusting the IPAdapter's influence.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`start_at`**
    - Defines the starting point of the IPAdapter's application, allowing for gradual application over the input.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_at`**
    - Defines the ending point of the IPAdapter's application, allowing for precise control over its influence.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`unfold_batch`**
    - Determines whether the batch should be unfolded during the IPAdapter's application, affecting how inputs are processed.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`attn_mask`**
    - Optional. An attention mask that can be used to focus the IPAdapter's application on specific parts of the input.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`model`**
    - The modified model after applying the IPAdapter, reflecting the adjustments made to its embeddings or features.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `Reroute,ADE_AnimateDiffLoaderWithContext,KSampler,ModelMergeSimple,IPAdapterApplyFaceID,KSampler (Efficient),IPAdapterApply,SelfAttentionGuidance,KSamplerAdvanced,ReroutePrimitive|pysssss`

The IPAdapterApply node applies an IPAdapter to modify the embeddings or features of a model based on the provided inputs, including an IPAdapter model, CLIP vision features, an image, and optionally an attention mask. It outputs a modified model that incorporates the adjustments made by the IPAdapter, enabling enhanced or tailored image processing and generation tasks.
## Source code
```python
class IPAdapterApply:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "ipadapter": ("IPADAPTER", ),
                "clip_vision": ("CLIP_VISION",),
                "image": ("IMAGE",),
                "model": ("MODEL", ),
                "weight": ("FLOAT", { "default": 1.0, "min": -1, "max": 3, "step": 0.05 }),
                "noise": ("FLOAT", { "default": 0.0, "min": 0.0, "max": 1.0, "step": 0.01 }),
                "weight_type": (["original", "linear", "channel penalty"], ),
                "start_at": ("FLOAT", { "default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001 }),
                "end_at": ("FLOAT", { "default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001 }),
                "unfold_batch": ("BOOLEAN", { "default": False }),
            },
            "optional": {
                "attn_mask": ("MASK",),
            }
        }

    RETURN_TYPES = ("MODEL", )
    FUNCTION = "apply_ipadapter"
    CATEGORY = "ipadapter"

    def apply_ipadapter(self, ipadapter, model, weight, clip_vision=None, image=None, weight_type="original", noise=None, embeds=None, attn_mask=None, start_at=0.0, end_at=1.0, unfold_batch=False, insightface=None, faceid_v2=False, weight_v2=False):
        self.dtype = torch.float16 if comfy.model_management.should_use_fp16() else torch.float32
        self.device = comfy.model_management.get_torch_device()
        self.weight = weight
        self.is_full = "proj.3.weight" in ipadapter["image_proj"]
        self.is_portrait = "proj.2.weight" in ipadapter["image_proj"] and not "proj.3.weight" in ipadapter["image_proj"] and not "0.to_q_lora.down.weight" in ipadapter["ip_adapter"]
        self.is_faceid = self.is_portrait or "0.to_q_lora.down.weight" in ipadapter["ip_adapter"]
        self.is_plus = (self.is_full or "latents" in ipadapter["image_proj"] or "perceiver_resampler.proj_in.weight" in ipadapter["image_proj"])

        if self.is_faceid and not insightface:
            raise Exception('InsightFace must be provided for FaceID models.')

        output_cross_attention_dim = ipadapter["ip_adapter"]["1.to_k_ip.weight"].shape[1]
        self.is_sdxl = output_cross_attention_dim == 2048
        cross_attention_dim = 1280 if self.is_plus and self.is_sdxl and not self.is_faceid else output_cross_attention_dim
        clip_extra_context_tokens = 16 if self.is_plus or self.is_portrait else 4

        if embeds is not None:
            embeds = torch.unbind(embeds)
            clip_embed = embeds[0].cpu()
            clip_embed_zeroed = embeds[1].cpu()
        else:
            if self.is_faceid:
                insightface.det_model.input_size = (640,640) # reset the detection size
                face_img = tensorToNP(image)
                face_embed = []
                face_clipvision = []

                for i in range(face_img.shape[0]):
                    for size in [(size, size) for size in range(640, 128, -64)]:
                        insightface.det_model.input_size = size # TODO: hacky but seems to be working
                        face = insightface.get(face_img[i])
                        if face:
                            face_embed.append(torch.from_numpy(face[0].normed_embedding).unsqueeze(0))
                            face_clipvision.append(NPToTensor(insightface_face_align.norm_crop(face_img[i], landmark=face[0].kps, image_size=224)))

                            if 640 not in size:
                                print(f"\033[33mINFO: InsightFace detection resolution lowered to {size}.\033[0m")
                            break
                    else:
                        raise Exception('InsightFace: No face detected.')

                face_embed = torch.stack(face_embed, dim=0)
                image = torch.stack(face_clipvision, dim=0)

                neg_image = image_add_noise(image, noise) if noise > 0 else None
               
                if self.is_plus:
                    clip_embed = clip_vision.encode_image(image).penultimate_hidden_states
                    if noise > 0:
                        clip_embed_zeroed = clip_vision.encode_image(neg_image).penultimate_hidden_states
                    else:
                        clip_embed_zeroed = zeroed_hidden_states(clip_vision, image.shape[0])
                    
                    # TODO: check noise to the uncods too
                    face_embed_zeroed = torch.zeros_like(face_embed)
                else:
                    clip_embed = face_embed
                    clip_embed_zeroed = torch.zeros_like(clip_embed)
            else:
                if image.shape[1] != image.shape[2]:
                    print("\033[33mINFO: the IPAdapter reference image is not a square, CLIPImageProcessor will resize and crop it at the center. If the main focus of the picture is not in the middle the result might not be what you are expecting.\033[0m")

                clip_embed = clip_vision.encode_image(image)
                neg_image = image_add_noise(image, noise) if noise > 0 else None
                
                if self.is_plus:
                    clip_embed = clip_embed.penultimate_hidden_states
                    if noise > 0:
                        clip_embed_zeroed = clip_vision.encode_image(neg_image).penultimate_hidden_states
                    else:
                        clip_embed_zeroed = zeroed_hidden_states(clip_vision, image.shape[0])
                else:
                    clip_embed = clip_embed.image_embeds
                    if noise > 0:
                        clip_embed_zeroed = clip_vision.encode_image(neg_image).image_embeds
                    else:
                        clip_embed_zeroed = torch.zeros_like(clip_embed)

        clip_embeddings_dim = clip_embed.shape[-1]

        self.ipadapter = IPAdapter(
            ipadapter,
            cross_attention_dim=cross_attention_dim,
            output_cross_attention_dim=output_cross_attention_dim,
            clip_embeddings_dim=clip_embeddings_dim,
            clip_extra_context_tokens=clip_extra_context_tokens,
            is_sdxl=self.is_sdxl,
            is_plus=self.is_plus,
            is_full=self.is_full,
            is_faceid=self.is_faceid,
        )
        
        self.ipadapter.to(self.device, dtype=self.dtype)

        if self.is_faceid and self.is_plus:
            image_prompt_embeds = self.ipadapter.get_image_embeds_faceid_plus(face_embed.to(self.device, dtype=self.dtype), clip_embed.to(self.device, dtype=self.dtype), weight_v2, faceid_v2)
            uncond_image_prompt_embeds = self.ipadapter.get_image_embeds_faceid_plus(face_embed_zeroed.to(self.device, dtype=self.dtype), clip_embed_zeroed.to(self.device, dtype=self.dtype), weight_v2, faceid_v2)
        else:
            image_prompt_embeds, uncond_image_prompt_embeds = self.ipadapter.get_image_embeds(clip_embed.to(self.device, dtype=self.dtype), clip_embed_zeroed.to(self.device, dtype=self.dtype))

        image_prompt_embeds = image_prompt_embeds.to(self.device, dtype=self.dtype)
        uncond_image_prompt_embeds = uncond_image_prompt_embeds.to(self.device, dtype=self.dtype)

        work_model = model.clone()

        if attn_mask is not None:
            attn_mask = attn_mask.to(self.device)

        sigma_start = model.model.model_sampling.percent_to_sigma(start_at)
        sigma_end = model.model.model_sampling.percent_to_sigma(end_at)

        patch_kwargs = {
            "number": 0,
            "weight": self.weight,
            "ipadapter": self.ipadapter,
            "cond": image_prompt_embeds,
            "uncond": uncond_image_prompt_embeds,
            "weight_type": weight_type,
            "mask": attn_mask,
            "sigma_start": sigma_start,
            "sigma_end": sigma_end,
            "unfold_batch": unfold_batch,
        }

        if not self.is_sdxl:
            for id in [1,2,4,5,7,8]: # id of input_blocks that have cross attention
                set_model_patch_replace(work_model, patch_kwargs, ("input", id))
                patch_kwargs["number"] += 1
            for id in [3,4,5,6,7,8,9,10,11]: # id of output_blocks that have cross attention
                set_model_patch_replace(work_model, patch_kwargs, ("output", id))
                patch_kwargs["number"] += 1
            set_model_patch_replace(work_model, patch_kwargs, ("middle", 0))
        else:
            for id in [4,5,7,8]: # id of input_blocks that have cross attention
                block_indices = range(2) if id in [4, 5] else range(10) # transformer_depth
                for index in block_indices:
                    set_model_patch_replace(work_model, patch_kwargs, ("input", id, index))
                    patch_kwargs["number"] += 1
            for id in range(6): # id of output_blocks that have cross attention
                block_indices = range(2) if id in [3, 4, 5] else range(10) # transformer_depth
                for index in block_indices:
                    set_model_patch_replace(work_model, patch_kwargs, ("output", id, index))
                    patch_kwargs["number"] += 1
            for index in range(10):
                set_model_patch_replace(work_model, patch_kwargs, ("middle", 0, index))
                patch_kwargs["number"] += 1

        return (work_model, )

```
