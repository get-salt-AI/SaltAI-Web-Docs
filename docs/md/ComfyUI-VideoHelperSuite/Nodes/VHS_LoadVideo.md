# Load Video (Upload) 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_LoadVideo`
- Category: `Video Helper Suite 🎥🅥🅗🅢`
- Output node: `False`

The `VHS_LoadVideo` node is designed to load video files uploaded by the user, process them according to specified parameters such as frame rate, size, and selection criteria, and convert them into a format suitable for use within the ComfyUI environment. This involves frame extraction, optional resizing, and conversion from BGR to RGB color space, facilitating further processing or visualization.
## Input types
### Required
- **`video`**
    - Specifies the path to the video file to be processed. It is crucial for determining the source of the frames to be extracted and processed.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`force_rate`**
    - Determines the frame rate at which the video should be processed, allowing for control over the temporal resolution of the extracted frames.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`force_size`**
    - Indicates whether the video frames should be resized to a specific dimension, affecting the spatial resolution of the output.
    - Python dtype: `str`
    - Comfy dtype: `['Disabled', 'Custom Height', 'Custom Width', 'Custom', '256x?', '?x256', '256x256', '512x?', '?x512', '512x512']`
- **`custom_width`**
    - Specifies the desired width for frame resizing, directly influencing the aspect ratio and resolution of the output frames.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`custom_height`**
    - Specifies the desired height for frame resizing, directly influencing the aspect ratio and resolution of the output frames.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`frame_load_cap`**
    - Sets a limit on the number of frames to be processed and loaded, enabling efficient memory usage and processing time management.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`skip_first_frames`**
    - Allows skipping a specified number of initial frames, useful for bypassing unneeded content at the beginning of the video.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`select_every_nth`**
    - Enables the selection of every Nth frame from the video, providing a mechanism to thin out the frame sequence for specific use cases.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`image`**
    - The processed frames from the video, converted to a format compatible with ComfyUI, ready for further processing or visualization.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`int`**
    - The total number of frames processed from the video.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`vhs_audio`**
    - The audio extracted from the video, if any, processed for use within the ComfyUI environment.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `VHS_AUDIO`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PreviewImage,VHS_VideoCombine,VAEEncode,EmptyLatentImage,CannyEdgePreprocessor,ImageScaleToTotalPixels,ImageResize+,TilePreprocessor,LineArtPreprocessor,DWPreprocessor`


## Source code
```python
class LoadVideoUpload:
    @classmethod
    def INPUT_TYPES(s):
        input_dir = folder_paths.get_input_directory()
        files = []
        for f in os.listdir(input_dir):
            if os.path.isfile(os.path.join(input_dir, f)):
                file_parts = f.split('.')
                if len(file_parts) > 1 and (file_parts[-1] in video_extensions):
                    files.append(f)
        return {"required": {
                    "video": (sorted(files),),
                     "force_rate": ("INT", {"default": 0, "min": 0, "max": 24, "step": 1}),
                     "force_size": (["Disabled", "Custom Height", "Custom Width", "Custom", "256x?", "?x256", "256x256", "512x?", "?x512", "512x512"],),
                     "custom_width": ("INT", {"default": 512, "min": 0, "step": 8}),
                     "custom_height": ("INT", {"default": 512, "min": 0, "step": 8}),
                     "frame_load_cap": ("INT", {"default": 0, "min": 0, "step": 1}),
                     "skip_first_frames": ("INT", {"default": 0, "min": 0, "step": 1}),
                     "select_every_nth": ("INT", {"default": 1, "min": 1, "step": 1}),
                     },}

    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢"

    RETURN_TYPES = ("IMAGE", "INT", "VHS_AUDIO", )
    RETURN_NAMES = ("IMAGE", "frame_count", "audio",)
    FUNCTION = "load_video"

    def load_video(self, **kwargs):
        kwargs['video'] = folder_paths.get_annotated_filepath(kwargs['video'].strip("\""))
        return load_video_cv(**kwargs)

    @classmethod
    def IS_CHANGED(s, video, **kwargs):
        image_path = folder_paths.get_annotated_filepath(video)
        return calculate_file_hash(image_path)

    @classmethod
    def VALIDATE_INPUTS(s, video, force_size, **kwargs):
        if not folder_paths.exists_annotated_filepath(video):
            return "Invalid video file: {}".format(video)
        return True

```
