# SVD_img2vid_Conditioning
## Documentation
- Class name: `SVD_img2vid_Conditioning`
- Category: `conditioning/video_models`
- Output node: `False`

This node is designed for video generation conditioning, utilizing a combination of CLIP vision embeddings, an initial image, and a VAE model to generate conditioning data for video frames. It adjusts the dimensions of the input image and applies video-specific parameters such as frame count, motion bucket ID, and frames per second (fps) to create a tailored conditioning environment for generating videos.
## Input types
### Required
- **`clip_vision`**
    - Represents the CLIP vision model used to extract visual features from the initial image, playing a crucial role in understanding the content and context of the image for video generation.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP_VISION`
- **`init_image`**
    - The initial image serves as the starting point for video generation, providing the visual context and content that will be expanded upon in the video frames.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`vae`**
    - A Variational Autoencoder (VAE) model used to encode the initial image and any subsequent frames into a latent space, facilitating the generation of coherent and continuous video content.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`width`**
    - Specifies the width of the video frames to be generated, allowing for customization of the video's resolution.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Specifies the height of the video frames to be generated, complementing the width parameter to define the video's resolution.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`video_frames`**
    - Determines the number of frames to be generated for the video, directly influencing the video's length and content.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`motion_bucket_id`**
    - A unique identifier for the motion bucket, which categorizes the type of motion to be applied to the video frames, enhancing the dynamism and realism of the generated video.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`fps`**
    - Frames per second (fps) setting for the video, dictating the playback speed and smoothness of the generated video content.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`augmentation_level`**
    - Controls the level of augmentation applied to the initial image, introducing variability and enhancing the realism of the generated video frames.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The negative conditioning data, serving as a contrast to the positive data, potentially used for refining the generation process.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`latent`**
    - The latent representation of the video frames, encoded into a compact form that can be used for further processing or generation steps.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,Prompts Everywhere,KSamplerAdvanced,SeargeSDXLSamplerV3`


## Source code
```python
class SVD_img2vid_Conditioning:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "clip_vision": ("CLIP_VISION",),
                              "init_image": ("IMAGE",),
                              "vae": ("VAE",),
                              "width": ("INT", {"default": 1024, "min": 16, "max": nodes.MAX_RESOLUTION, "step": 8}),
                              "height": ("INT", {"default": 576, "min": 16, "max": nodes.MAX_RESOLUTION, "step": 8}),
                              "video_frames": ("INT", {"default": 14, "min": 1, "max": 4096}),
                              "motion_bucket_id": ("INT", {"default": 127, "min": 1, "max": 1023}),
                              "fps": ("INT", {"default": 6, "min": 1, "max": 1024}),
                              "augmentation_level": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 10.0, "step": 0.01})
                             }}
    RETURN_TYPES = ("CONDITIONING", "CONDITIONING", "LATENT")
    RETURN_NAMES = ("positive", "negative", "latent")

    FUNCTION = "encode"

    CATEGORY = "conditioning/video_models"

    def encode(self, clip_vision, init_image, vae, width, height, video_frames, motion_bucket_id, fps, augmentation_level):
        output = clip_vision.encode_image(init_image)
        pooled = output.image_embeds.unsqueeze(0)
        pixels = comfy.utils.common_upscale(init_image.movedim(-1,1), width, height, "bilinear", "center").movedim(1,-1)
        encode_pixels = pixels[:,:,:,:3]
        if augmentation_level > 0:
            encode_pixels += torch.randn_like(pixels) * augmentation_level
        t = vae.encode(encode_pixels)
        positive = [[pooled, {"motion_bucket_id": motion_bucket_id, "fps": fps, "augmentation_level": augmentation_level, "concat_latent_image": t}]]
        negative = [[torch.zeros_like(pooled), {"motion_bucket_id": motion_bucket_id, "fps": fps, "augmentation_level": augmentation_level, "concat_latent_image": torch.zeros_like(t)}]]
        latent = torch.zeros([video_frames, 4, height // 8, width // 8])
        return (positive, negative, {"samples":latent})

```
