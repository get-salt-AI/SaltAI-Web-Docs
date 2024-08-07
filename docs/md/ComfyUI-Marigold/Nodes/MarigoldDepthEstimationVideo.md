---
tags:
- DepthMap
- DepthMapEstimation
- Image
---

# MarigoldDepthEstimationVideo
## Documentation
- Class name: `MarigoldDepthEstimationVideo`
- Category: `Marigold`
- Output node: `False`

The MarigoldDepthEstimationVideo node is designed for depth estimation in videos using a diffusion-based approach. It incorporates optical flow for enhanced frame consistency, aiming to produce more accurate and coherent depth maps across video sequences. This node is an experimental version that extends the capabilities of monocular depth estimation to video content, leveraging advanced techniques for improved depth perception and visual quality.
## Input types
### Required
- **`image`**
    - A single image from a video sequence to be processed for depth estimation. The node uses this image along with optical flow to generate a depth map, ensuring consistency between frames.
    - Comfy dtype: `IMAGE`
    - Python dtype: `Image`
- **`seed`**
    - unknown
    - Comfy dtype: `INT`
    - Python dtype: `unknown`
- **`first_frame_denoise_steps`**
    - The number of denoising steps to apply specifically for the first frame in a video sequence, affecting the initial depth map's accuracy and processing time.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`first_frame_n_repeat`**
    - Specifies the number of iterations to ensemble for the first frame's depth map, aiming to enhance the depth estimation accuracy for the video's starting point.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`n_repeat_batch_size`**
    - Determines how many of the n_repeats are processed as a batch, allowing for adjustments based on available VRAM for potentially faster processing.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`invert`**
    - A boolean flag indicating whether to invert the depth map's color scheme, where the default setting has black representing the foreground.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`keep_model_loaded`**
    - Indicates whether the Marigold model should remain loaded between frames to optimize processing time for video sequences.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`scheduler`**
    - unknown
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `unknown`
- **`normalize`**
    - A boolean flag that specifies whether the output depth maps should be normalized, affecting the visual representation of depth.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`denoise_steps`**
    - unknown
    - Comfy dtype: `INT`
    - Python dtype: `unknown`
- **`flow_warping`**
    - Enables the use of optical flow for warping depth maps between frames, enhancing the consistency and accuracy of depth estimation across the video.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`flow_depth_mix`**
    - Determines the blending ratio between the depth map generated from the current frame and the warped depth map from the previous frame, affecting the smoothness and consistency of the video output.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`noise_ratio`**
    - Specifies the ratio of noise to signal in the depth estimation process, influencing the model's behavior and the quality of the depth maps.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`dtype`**
    - Defines the data type used during processing, such as fp16 or fp32, impacting VRAM usage and potentially the quality of the depth estimation.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
### Optional
- **`model`**
    - Specifies the Marigold model to be used for depth estimation, allowing selection between the standard and LCM versions of Marigold. This choice influences the depth estimation process and the quality of the output depth maps.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
## Output types
- **`ensembled_image`**
    - Comfy dtype: `IMAGE`
    - The output ensembled depth map image, representing the combined result of depth estimation across multiple frames for enhanced accuracy and consistency.
    - Python dtype: `Image`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MarigoldDepthEstimationVideo:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {  
            "image": ("IMAGE", ),
            "seed": ("INT", {"default": 123,"min": 0, "max": 0xffffffffffffffff, "step": 1}),
            "first_frame_denoise_steps": ("INT", {"default": 4, "min": 1, "max": 4096, "step": 1}),
            "first_frame_n_repeat": ("INT", {"default": 1, "min": 1, "max": 4096, "step": 1}),
            "n_repeat_batch_size": ("INT", {"default": 1, "min": 1, "max": 4096, "step": 1}),          
            "invert": ("BOOLEAN", {"default": True}),
            "keep_model_loaded": ("BOOLEAN", {"default": True}),
            "scheduler": (
            [   
                'DDIMScheduler',
                'DDPMScheduler',
                'PNDMScheduler',
                'DEISMultistepScheduler',
                'LCMScheduler',
            ], {
               "default": 'DEISMultistepScheduler'
            }),
            "normalize": ("BOOLEAN", {"default": True}),
            "denoise_steps": ("INT", {"default": 4, "min": 1, "max": 4096, "step": 1}),
            "flow_warping": ("BOOLEAN", {"default": True}),
            "flow_depth_mix": ("FLOAT", {"default": 0.3, "min": 0.0, "max": 1.0, "step": 0.05}),
            "noise_ratio": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
            "dtype": (
            [   
                'fp16',
                'bf16',
                'fp32',
            ], {
               "default": 'fp16'
            }),
            },
            "optional": {
                "model": (
            [   
                'Marigold',
                'marigold-lcm-v1-0',  
            ], {
               "default": 'Marigold'
            }),
            }
            
            }
    
    RETURN_TYPES = ("IMAGE",)
    RETURN_NAMES =("ensembled_image",)
    FUNCTION = "process"
    CATEGORY = "Marigold"
    DESCRIPTION = """
Diffusion-based monocular depth estimation:  
https://github.com/prs-eth/Marigold  

This node is experimental version that includes optical flow  
for video consistency between frames.  

- denoise_steps: steps per depth map, increase for accuracy in exchange of processing time
- n_repeat: amount of iterations to be ensembled into single depth map
- n_repeat_batch_size: how many of the n_repeats are processed as a batch,  
if you have the VRAM this can match the n_repeats for faster processing  
- model: Marigold or it's LCM version marigold-lcm-v1-0  
For the LCM model use around 4 steps and the LCMScheduler  
- scheduler: Different schedulers give bit different results  
- invert: marigold by default produces depth map where black is front,  
for controlnets etc. we want the opposite.  
- regularizer_strength, reduction_method, max_iter, tol (tolerance) are settings   
for the ensembling process, generally do not touch.  
"""

    def process(self, image, seed, first_frame_denoise_steps, denoise_steps, first_frame_n_repeat, keep_model_loaded, invert,
                n_repeat_batch_size, dtype, scheduler, normalize, flow_warping, flow_depth_mix, noise_ratio, model="Marigold"):
        batch_size = image.shape[0]

        precision = convert_dtype(dtype)
       
        device = model_management.get_torch_device()
        torch.manual_seed(seed)

        image = image.permute(0, 3, 1, 2).to(device).to(dtype=precision)
        if normalize:
            image = image * 2.0 - 1.0
        
        if flow_warping:
            from .marigold.util.flow_estimation import FlowEstimator
            flow_estimator = FlowEstimator(os.path.join(script_directory, "gmflow", "gmflow_things-e9887eda.pth"), device)
        diffusers_model_path = os.path.join(folder_paths.models_dir,'diffusers')
        if model == "Marigold":
            folders_to_check = [
                os.path.join(script_directory,"checkpoints","Marigold_v1_merged",),
                os.path.join(script_directory,"checkpoints","Marigold",),
                os.path.join(diffusers_model_path,"Marigold_v1_merged"),
                os.path.join(diffusers_model_path,"Marigold")
            ]
        elif model == "marigold-lcm-v1-0":
            folders_to_check = [
                os.path.join(diffusers_model_path,"marigold-lcm-v1-0"),
                os.path.join(diffusers_model_path,"checkpoints","marigold-lcm-v1-0")
            ]
        self.custom_config = {
            "model": model,
            "dtype": dtype,
            "scheduler": scheduler,
        }
        if not hasattr(self, 'marigold_pipeline') or self.marigold_pipeline is None or self.current_config != self.custom_config:
            self.current_config = self.custom_config
            # Load the model only if it hasn't been loaded before
            checkpoint_path = None
            for folder in folders_to_check:
                potential_path = os.path.join(script_directory, folder)
                if os.path.exists(potential_path):
                    checkpoint_path = potential_path
                    break
            to_ignore = ["*.bin", "*fp16*"]            
            if checkpoint_path is None:
                if model == "Marigold":
                    try:
                        from huggingface_hub import snapshot_download
                        checkpoint_path = os.path.join(diffusers_model_path, "Marigold")
                        snapshot_download(repo_id="Bingxin/Marigold", ignore_patterns=to_ignore, local_dir=checkpoint_path, local_dir_use_symlinks=False)  
                    except:
                        raise FileNotFoundError(f"No checkpoint directory found at {checkpoint_path}")
                if model == "marigold-lcm-v1-0":
                    try:
                        from huggingface_hub import snapshot_download
                        checkpoint_path = os.path.join(diffusers_model_path, "marigold-lcm-v1-0")
                        snapshot_download(repo_id="prs-eth/marigold-lcm-v1-0", ignore_patterns=to_ignore, local_dir=checkpoint_path, local_dir_use_symlinks=False)  
                    except:
                        raise FileNotFoundError(f"No checkpoint directory found at {checkpoint_path}")
            
            self.marigold_pipeline = MarigoldPipeline.from_pretrained(checkpoint_path, enable_xformers=False, empty_text_embed=empty_text_embed, noise_scheduler_type=scheduler)
            self.marigold_pipeline = self.marigold_pipeline.to(precision).to(device)
            self.marigold_pipeline.unet.eval()
        pbar = comfy.utils.ProgressBar(batch_size)

        out = []
        for i in range(batch_size):
            if flow_warping:
                current_image = image[i]
                prev_image = image[i-1]
                flow = flow_estimator.estimate_flow(prev_image.to(torch.float32), current_image.to(torch.float32))
            if i == 0 or not flow_warping:
                # Duplicate the current image n_repeat times
                duplicated_batch = image[i].unsqueeze(0).repeat(first_frame_n_repeat, 1, 1, 1)
                # Process the duplicated batch in sub-batches
                depth_maps = []
                for j in range(0, first_frame_n_repeat, n_repeat_batch_size):
                    # Get the current sub-batch
                    sub_batch = duplicated_batch[j:j + n_repeat_batch_size]
                    
                    # Process the sub-batch
                    depth_maps_sub_batch = self.marigold_pipeline(sub_batch, num_inference_steps=first_frame_denoise_steps, show_pbar=False)
                    
                    # Process each depth map in the sub-batch if necessary
                    for depth_map in depth_maps_sub_batch:
                        depth_map = torch.clip(depth_map, -1.0, 1.0)
                        depth_map = (depth_map + 1.0) / 2.0
                        depth_maps.append(depth_map)
                
                depth_predictions = torch.cat(depth_maps, dim=0).squeeze()
                
                del duplicated_batch, depth_maps_sub_batch
                comfy.model_management.soft_empty_cache()

                # Test-time ensembling
                if first_frame_n_repeat > 1:
                    depth_map, pred_uncert = ensemble_depths(
                        depth_predictions,
                        regularizer_strength=0.02,
                        max_iter=5,
                        tol=1e-3,
                        reduction="median",
                        max_res=None,
                        device=device,
                    )
                    prev_depth_map = torch.clip(depth_map, 0.0, 1.0)                
                    depth_map = depth_map.unsqueeze(2).repeat(1, 1, 3)
                    out.append(depth_map)
                    pbar.update(1)
                else:
                    prev_depth_map = torch.clip(depth_map[0], 0.0, 1.0)                
                    depth_map = depth_map[0].unsqueeze(2).repeat(1, 1, 3)
                    out.append(depth_map)
                    pbar.update(1)
                
            else:
                #idea and original implementation from https://github.com/pablodawson/Marigold-Video
                warped_depth_map = FlowEstimator.warp_with_flow(flow, prev_depth_map).to(precision).to(device)                   
                warped_depth_map = (warped_depth_map + 1.0) / 2.0
                assert warped_depth_map.min() >= -1.0 and warped_depth_map.max() <= 1.0
                depth_predictions = self.marigold_pipeline(current_image.unsqueeze(0), init_depth_latent=warped_depth_map.unsqueeze(0).repeat(3, 1, 1).unsqueeze(0),
                                                            noise_ratio=noise_ratio, num_inference_steps=denoise_steps, show_pbar=False)
                
                warped_depth_map = warped_depth_map / warped_depth_map.max()
                depth_out = flow_depth_mix * depth_predictions + (1 - flow_depth_mix) * warped_depth_map
                depth_out = torch.clip(depth_out, 0.0, 1.0)

                prev_depth_map = depth_out.squeeze()
                depth_out = depth_out.squeeze().unsqueeze(2).repeat(1, 1, 3)
                
                out.append(depth_out)
                pbar.update(1)

                del depth_predictions, warped_depth_map
        if invert:
            outstack = 1.0 - torch.stack(out, dim=0).cpu().to(torch.float32)
        else:
            outstack = torch.stack(out, dim=0).cpu().to(torch.float32)
        if not keep_model_loaded:
            self.marigold_pipeline = None
            model_management.soft_empty_cache()
        return (outstack,)

```
