---
tags:
- AnimationScheduling
- FrameInterpolation
- VisualEffects
---

# FILM VFI
## Documentation
- Class name: `FILM VFI`
- Category: `ComfyUI-Frame-Interpolation/VFI`
- Output node: `False`

The FILM VFI node specializes in frame interpolation, leveraging deep learning models to generate intermediate frames between two given images. It aims to enhance video fluidity and detail by accurately predicting and synthesizing frames that could logically exist between the original frames, thus achieving smoother motion transitions and improved visual quality.
## Input types
### Required
- **`ckpt_name`**
    - Specifies the checkpoint name for the FILM VFI model, determining the specific pretrained model weights to be used for frame interpolation.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `List[str]`
- **`frames`**
    - The sequence of images to be interpolated, serving as the input frames for generating intermediate frames.
    - Comfy dtype: `IMAGE`
    - Python dtype: `torch.Tensor`
- **`clear_cache_after_n_frames`**
    - Controls how often the system's cache is cleared during the interpolation process to manage memory usage effectively.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`multiplier`**
    - Defines the number of intermediate frames to be generated between each pair of input frames, affecting the smoothness of the output video.
    - Comfy dtype: `INT`
    - Python dtype: `int`
### Optional
- **`optional_interpolation_states`**
    - Optional states that can influence the frame skipping logic during interpolation, allowing for more customized processing.
    - Comfy dtype: `INTERPOLATION_STATES`
    - Python dtype: `InterpolationStateList`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The output images generated by the FILM VFI model, representing the interpolated frames that enhance the fluidity and detail of the original video sequence.
    - Python dtype: `List[torch.Tensor]`
## Usage tips
- Infra type: `GPU`
- Common nodes:
    - [VHS_VideoCombine](../../ComfyUI-VideoHelperSuite/Nodes/VHS_VideoCombine.md)
    - [SaveAnimatedWEBP](../../Comfy/Nodes/SaveAnimatedWEBP.md)



## Source code
```python
class FILM_VFI:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "ckpt_name": (["film_net_fp32.pt"], ),
                "frames": ("IMAGE", ),
                "clear_cache_after_n_frames": ("INT", {"default": 10, "min": 1, "max": 1000}),
                "multiplier": ("INT", {"default": 2, "min": 2, "max": 1000}),
            },
            "optional": {
                "optional_interpolation_states": ("INTERPOLATION_STATES", )
            }
        }
    
    RETURN_TYPES = ("IMAGE", )
    FUNCTION = "vfi"
    CATEGORY = "ComfyUI-Frame-Interpolation/VFI"        

    def vfi(
        self,
        ckpt_name: typing.AnyStr,
        frames: torch.Tensor,
        clear_cache_after_n_frames = 10,
        multiplier: typing.SupportsInt = 2,
        optional_interpolation_states: InterpolationStateList = None,
        **kwargs
    ):
        interpolation_states = optional_interpolation_states
        model_path = load_file_from_github_release(MODEL_TYPE, ckpt_name)
        model = torch.jit.load(model_path, map_location='cpu')
        model.eval()
        model = model.to(DEVICE)
        dtype = torch.float32

        frames = preprocess_frames(frames)
        number_of_frames_processed_since_last_cleared_cuda_cache = 0
        output_frames = []
        
        if type(multiplier) == int:
            multipliers = [multiplier] * len(frames)
        else:
            multipliers = list(map(int, multiplier))
            multipliers += [2] * (len(frames) - len(multipliers) - 1)
        for frame_itr in range(len(frames) - 1): # Skip the final frame since there are no frames after it
            if interpolation_states is not None and interpolation_states.is_frame_skipped(frame_itr):
                continue
            #Ensure that input frames are in fp32 - the same dtype as model
            frame_0 = frames[frame_itr:frame_itr+1].to(DEVICE).float()
            frame_1 = frames[frame_itr+1:frame_itr+2].to(DEVICE).float()
            relust = inference(model, frame_0, frame_1, multipliers[frame_itr] - 1)
            output_frames.extend([frame.detach().cpu().to(dtype=dtype) for frame in relust[:-1]])

            number_of_frames_processed_since_last_cleared_cuda_cache += 1
            # Try to avoid a memory overflow by clearing cuda cache regularly
            if number_of_frames_processed_since_last_cleared_cuda_cache >= clear_cache_after_n_frames:
                print("Comfy-VFI: Clearing cache...", end = ' ')
                soft_empty_cache()
                number_of_frames_processed_since_last_cleared_cuda_cache = 0
                print("Done cache clearing")
            gc.collect()
        
        output_frames.append(frames[-1:].to(dtype=dtype)) # Append final frame
        output_frames = [frame.cpu() for frame in output_frames] #Ensure all frames are in cpu
        out = torch.cat(output_frames, dim=0)
        # clear cache for courtesy
        print("Comfy-VFI: Final clearing cache...", end = ' ')
        soft_empty_cache()
        print("Done cache clearing")
        return (postprocess_frames(out), )

```
