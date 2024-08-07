---
tags:
- Audio
---

# Audio Scheduled Pitch Shift
## Documentation
- Class name: `SaltAudioPitchShiftScheduled`
- Category: `SALT/AudioViz/Audio/Effect`
- Output node: `False`

This node is designed for applying a scheduled pitch shift to audio data. It allows for dynamic adjustments to the pitch over time, based on a predefined schedule, enabling more complex and nuanced audio transformations than a static pitch shift.
## Input types
### Required
- **`audio`**
    - The audio data to be processed. It is the primary input for pitch shifting operations, determining the raw sound material to be modified.
    - Comfy dtype: `AUDIO`
    - Python dtype: `bytes`
- **`schedule`**
    - A list or array defining the pitch shift values over time. This schedule dictates how the pitch is altered throughout the audio, allowing for dynamic and varied pitch changes.
    - Comfy dtype: `LIST`
    - Python dtype: `List[float] or np.ndarray`
### Optional
- **`interpolate`**
    - A boolean flag indicating whether to interpolate between scheduled pitch values for smoother transitions or to apply them directly as discrete steps.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`audio`**
    - Comfy dtype: `AUDIO`
    - The resulting audio data after applying the scheduled pitch shifts. It reflects the dynamic pitch adjustments made according to the input schedule.
    - Python dtype: `bytes`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SaltAudioPitchShiftScheduled:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "audio": ("AUDIO", {}),
                "schedule": ("LIST", {"element_type": "FLOAT"}),
            },
            "optional": {
                "interpolate": ("BOOLEAN", {"default": False}),
            }
        }

    RETURN_TYPES = ("AUDIO",)
    RETURN_NAMES = ("audio",)
    FUNCTION = "shift_pitch_advanced"
    CATEGORY = f"{MENU_NAME}/{SUB_MENU_NAME}/Audio/Effect"

    @staticmethod
    def shift_pitch_advanced(audio_bytes, schedule, interpolate=False):
        TEMP = tempfile.gettempdir()
        os.makedirs(TEMP, exist_ok=True)

        temp_input_path = tempfile.mktemp(suffix='.wav', dir=TEMP)
        temp_output_path = tempfile.mktemp(suffix='.wav', dir=TEMP)

        with open(temp_input_path, 'wb') as f:
            f.write(audio_bytes)

        audio = AudioSegment.from_file(temp_input_path)
        frame_rate = audio.frame_rate
        total_frames = len(audio.get_array_of_samples())

        # Schedule processing: interpolate or repeat to match audio length
        if interpolate:
            x = np.linspace(0, total_frames, num=len(schedule), endpoint=True)
            f = interp1d(x, schedule, kind='linear', fill_value="extrapolate")
            pitch_schedule = f(np.arange(total_frames))
        else:
            pitch_schedule = np.tile(schedule, int(np.ceil(total_frames / len(schedule))))[:total_frames]

        # Process audio in chunks and apply pitch shift
        processed_audio = AudioSegment.empty()
        chunk_duration_ms = 100  # Duration of each chunk in milliseconds
        for i in range(0, len(audio), chunk_duration_ms):
            chunk = audio[i:i+chunk_duration_ms]
            semitones = pitch_schedule[int(i / chunk_duration_ms * frame_rate)]
            processed_chunk = chunk._spawn(chunk.raw_data, overrides={
                "frame_rate": int(chunk.frame_rate * 2**(semitones/12.0))
            }).set_frame_rate(frame_rate)
            processed_audio += processed_chunk

        # Export processed audio
        processed_audio.export(temp_output_path, format="wav")

        with open(temp_output_path, 'rb') as f:
            pitch_shifted_audio = f.read()

        os.remove(temp_input_path)
        os.remove(temp_output_path)

        return (pitch_shifted_audio,)

```
