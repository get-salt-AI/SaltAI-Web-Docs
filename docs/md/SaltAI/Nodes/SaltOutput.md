---
tags:
- Audio
- SaltNodes
---

# Salt Workflow Output
## Documentation
- Class name: `SaltOutput`
- Category: `SALT/IO`
- Output node: `True`

The SaltOutput node is designed for handling the output process within the Salt AI framework, specifically focusing on generating and managing output data. It encapsulates the functionality for formatting output data, determining its type, and organizing it into a structured UI-friendly format for further use or display.
## Input types
### Required
- **`output_name`**
    - Specifies the name of the output, which is used for identification and organization purposes. It plays a crucial role in how the output is labeled and accessed in subsequent operations.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`output_desc`**
    - Provides a description for the output, offering context or additional details about the nature or content of the output data.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`output_type`**
    - Defines the type of the output data, such as image, audio, or text formats, influencing how the data is processed and presented.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`output_data`**
    - The actual data to be outputted, which can vary in type (e.g., image, audio, text) based on the output_type parameter.
    - Comfy dtype: `*`
    - Python dtype: `Union[torch.Tensor, str, bytes]`
### Optional
- **`video_audio`**
    - Optional audio data for video outputs, used when the output type is a video format that supports accompanying audio tracks.
    - Comfy dtype: `AUDIO`
    - Python dtype: `bytes`
- **`animation_fps`**
    - Specifies the frames per second for animated outputs, affecting the playback speed and smoothness of animations.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`animation_quality`**
    - Determines the quality level of the animation, allowing for a balance between visual fidelity and file size.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
## Output types
- **`ui`**
    - A structured dictionary containing UI-friendly formatted output data, including metadata and potentially preview images for supported types.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SaltOutput:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "output_name": ("STRING", {}),
                "output_desc": ("STRING", {}),
                "output_type": (
                    ["PNG", "JPEG", "GIF", "WEBP", "AVI", "MP4", "WEBM", "MP3", "WAV", "STRING"],
                ),
                "output_data": (WILDCARD, {}),
            },
            "optional": {
                "video_audio": ("AUDIO", {}),
                "animation_fps": ("INT", {"min": 1, "max": 60, "default": 8}),
                "animation_quality": (["DEFAULT", "HIGH"],),
            },
            "hidden": {"unique_id": "UNIQUE_ID", "output_subdir": None},
        }

    OUTPUT_NODE = True
    RETURN_TYPES = ()

    FUNCTION = "output"
    CATEGORY = "SALT/IO"

    def output(
        self,
        output_name,
        output_desc,
        output_type,
        output_data,
        video_audio=None,
        animation_fps=8,
        animation_quality="DEFAULT",
        unique_id=0,
        output_subdir=None
    ):
        is_asset = False
        asset_id = str(uuid.uuid4())

        # Determine if valid type
        if not isinstance(output_data, torch.Tensor) and not isinstance(output_data, str) and not isinstance(output_data, bytes) and not is_lambda(output_data):
            errmsg = f"Unsupported output_data supplied `{str(type(output_data).__name__)}`. Please provide `IMAGE` (torch.Tensor), `STRING` (str), or `AUDIO` (bytes) input."
            logger.error(errmsg)
            raise ValueError(errmsg)
        
        # Support VHS audio
        if output_type in ["AVI", "MP4", "WEBM", "MP3", "WAV"]:
            if video_audio is not None and is_lambda(video_audio):
                video_audio = video_audio()
            if is_lambda(output_data):
                output_data = output_data()
        
        if video_audio and not isinstance(video_audio, bytes):
            errmsg = f"Unsupported video_audio supplied `{str(type(output_data).__name__)}. Please provide `AUDIO` (bytes)"
            logger.error(errmsg)
            raise ValueError(errmsg)

        # Is asset? I may have misunderstood this part
        if output_type in ["GIF", "WEBP", "AVI", "MP4", "WEBM", "MP3", "WAV"]:
            is_asset = True

        # Determine output name, and sanitize if input (for filesystem)
        if output_name.strip() == "":
            output_name = "output_" + str(unique_id)
        else:
            output_name = sanitize_filename(output_name)

        # Create output dir based on uuid4
        subfolder = os.path.join(output_subdir or '', asset_id)
        output_path = os.path.join(folder_paths.get_output_directory(), subfolder)

        os.makedirs(output_path, exist_ok=True)
        if not os.path.exists(output_path):
            errmsg = f"Unable to create output directory `{output_path}`"
            logger.warning(errmsg)

        results = []
        if output_type in ("PNG", "JPEG") and isinstance(output_data, torch.Tensor):
            # Save all images in the tensor batch as specified by output_type
            try:
                for index, img in enumerate(output_data):
                    pil_image = tensor2pil(img)
                    file_prefix = output_name.strip().replace(" ", "_")
                    file_ext = f".{output_type.lower()}"
                    filename = f"{file_prefix}_{index:04d}{file_ext}"
                    image_path = os.path.join(output_path, filename)
                    pil_image.save(image_path, output_type)
                    results.append({
                        "filename": filename,
                        "subfolder": subfolder,
                        "type": "output"
                    })
                    if os.path.exists(image_path):
                        logger.info(f"Saved image to `{image_path}`")
                    else:
                        errmsg = f"Unable to save image to `{image_path}`"
                        logger.warning(errmsg)

            except Exception as e:
                errmsg = f"Unknown exception {e}"
                logger.error(errmsg)
                raise e

        if output_type in ["GIF", "WEBP", "AVI", "MP4", "WEBM"] and isinstance(output_data, torch.Tensor):
            # Save animation file
            filename = os.path.join(output_path, f"{output_name}.{output_type.lower()}")
            animator = ImageAnimator(
                output_data, fps=int(animation_fps), quality=animation_quality
            )
            animator.save_animation(filename, format=output_type, audio=video_audio)
            results.append({
                "filename": os.path.basename(filename),
                "subfolder": subfolder,
                "type": "output"
            })
            if os.path.exists(filename):
                logger.info(f"[SALT] Saved file to `{filename}`")
            else:
                errmsg = f"[SALT] Unable to save file to `{filename}`"
                logger.warning(errmsg)
        elif output_type in ["MP3", "WAV"] and isinstance(output_data, bytes):
            # Save audio file
            filename = os.path.join(output_path, f"{output_name}.{output_type.lower()}")

            audio_buffer = io.BytesIO(output_data)
            audio = AudioSegment.from_file(audio_buffer)

            if output_type == "MP3":
                audio.export(filename, format="mp3")
            else:
                audio.export(filename, format="wav")

            results.append({
                "filename": os.path.basename(filename),
                "subfolder": subfolder,
                "type": "output"
            })

            if os.path.exists(filename):
                logger.info(f"Saved file to `{filename}`")
            else:
                errmsg = f"Unable to save file to `{filename}`"
                logger.warning(errmsg)

        else:
            # Assume string output
            if output_type == "STRING":
                results.append(str(output_data))

        # Output Dictionary
        ui = {
            "ui": {
                "salt_metadata": [
                    {
                        "salt_id": unique_id,
                        "salt_reference_uuid": asset_id,
                        "salt_description": output_desc,
                        "salt_asset": is_asset,
                        "salt_file_extension": output_type,
                    }
                ],
                "salt_output": results
            }
        }

        # Assign images for previews of supported types
        if output_type in ["PNG", "GIF", "WEBP", "JPEG"] and results:
            ui["ui"].update({"images": results})

        # Print to log
        logger.info(f"[SaltOutput_{unique_id}] Output:")
        logger.data(ui, indent=4)
        
        return ui

```
