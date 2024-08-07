---
tags:
- Audio
---

# Audio Trim
## Documentation
- Class name: `SaltAudioTrim`
- Category: `SALT/AudioViz/Audio/Process`
- Output node: `False`

The SaltAudioTrim node is designed for trimming audio files to a specified start and end time, allowing for precise control over the segment of the audio that is of interest.
## Input types
### Required
- **`audio`**
    - The 'audio' parameter is the raw audio data that will be trimmed. It is essential for determining the segment of the audio file to be processed.
    - Comfy dtype: `AUDIO`
    - Python dtype: `bytes`
- **`start_time_seconds`**
    - The 'start_time_seconds' parameter specifies the starting point of the audio segment to trim, measured in seconds from the beginning of the audio file. It plays a crucial role in defining the portion of the audio to retain.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`end_time_seconds`**
    - The 'end_time_seconds' parameter defines the ending point of the audio segment to trim, measured in seconds. It is critical for delineating the end of the segment to be kept.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
## Output types
- **`audio`**
    - Comfy dtype: `AUDIO`
    - The output 'audio' is the trimmed segment of the original audio file, processed according to the specified start and end times.
    - Python dtype: `bytes`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SaltAudioTrim:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "audio": ("AUDIO", {}),
                "start_time_seconds": ("FLOAT", {"min": 0.0, "default": 0.0, "step": 0.01}),
                "end_time_seconds": ("FLOAT", {"min": 0.0, "step": 0.01}),
            },
        }

    RETURN_TYPES = ("AUDIO",)
    RETURN_NAMES = ("audio",)
    FUNCTION = "trim_audio"
    CATEGORY = f"{MENU_NAME}/{SUB_MENU_NAME}/Audio/Process"

    def trim_audio(cls, audio, start_time_seconds, end_time_seconds):
        audio_segment = AudioSegment.from_file(io.BytesIO(audio), format="wav")
        trimmed_audio_segment = audio_segment[start_time_seconds * 1000:end_time_seconds * 1000]
        return (get_buffer(trimmed_audio_segment),)

```
