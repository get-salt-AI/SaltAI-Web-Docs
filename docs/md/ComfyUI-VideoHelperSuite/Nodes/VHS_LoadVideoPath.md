# Load Video (Path) 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_LoadVideoPath`
- Category: `Video Helper Suite 🎥🅥🅗🅢`
- Output node: `False`

This node is designed to load video files from a specified path. It supports various customization options such as adjusting the frame rate, resizing the video dimensions, and selecting specific frames to load. This allows for flexible video processing tailored to the user's needs.
## Input types
### Required
- **`video`**
    - The path to the video file to be loaded. This parameter is crucial as it determines which video will be processed.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`force_rate`**
    - Specifies the frame rate to which the video should be adjusted. This can be used to standardize the frame rate across different videos.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`force_size`**
    - Defines the target size for the video, allowing for resizing to a specific dimension. It includes options like 'Disabled', 'Custom Height', 'Custom Width', 'Custom', and various fixed sizes.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`custom_width`**
    - Sets the custom width for the video resizing, providing control over the video's aspect ratio.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`custom_height`**
    - Sets the custom height for the video resizing, offering further customization of the video's dimensions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`frame_load_cap`**
    - Limits the number of frames to be loaded from the video, useful for processing shorter segments or reducing memory usage.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`skip_first_frames`**
    - Allows skipping a specified number of initial frames, useful for bypassing unneeded content at the beginning of a video.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`select_every_nth`**
    - Enables the selection of every Nth frame from the video, facilitating the reduction of the total number of frames processed.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The processed video frames as images.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`int`**
    - The total number of frames processed and loaded from the video.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`vhs_audio`**
    - The audio track extracted from the video, if any.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `VHS_AUDIO`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LoadVideoPath:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "video": ("STRING", {"default": "X://insert/path/here.mp4", "vhs_path_extensions": video_extensions}),
                "force_rate": ("INT", {"default": 0, "min": 0, "max": 24, "step": 1}),
                 "force_size": (["Disabled", "Custom Height", "Custom Width", "Custom", "256x?", "?x256", "256x256", "512x?", "?x512", "512x512"],),
                 "custom_width": ("INT", {"default": 512, "min": 0, "step": 8}),
                 "custom_height": ("INT", {"default": 512, "min": 0, "step": 8}),
                "frame_load_cap": ("INT", {"default": 0, "min": 0, "step": 1}),
                "skip_first_frames": ("INT", {"default": 0, "min": 0, "step": 1}),
                "select_every_nth": ("INT", {"default": 1, "min": 1, "step": 1}),
            },
        }

    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢"

    RETURN_TYPES = ("IMAGE", "INT", "VHS_AUDIO", )
    RETURN_NAMES = ("IMAGE", "frame_count", "audio",)
    FUNCTION = "load_video"

    def load_video(self, **kwargs):
        if kwargs['video'] is None or validate_path(kwargs['video']) != True:
            raise Exception("video is not a valid path: " + kwargs['video'])
        return load_video_cv(**kwargs)

    @classmethod
    def IS_CHANGED(s, video, **kwargs):
        return hash_path(video)

    @classmethod
    def VALIDATE_INPUTS(s, video, **kwargs):
        return validate_path(video, allow_none=True)

```
