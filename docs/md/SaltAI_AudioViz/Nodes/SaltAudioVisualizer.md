---
tags:
- Audio
---

# Audio Visualizer
## Documentation
- Class name: `SaltAudioVisualizer`
- Category: `SALT/AudioViz/Audio/Util`
- Output node: `True`

Provides a visual representation of audio data, enabling users to visualize the waveform or spectrum of audio clips. This can be particularly useful for analyzing the audio's structure, identifying specific parts, or simply for aesthetic visualization purposes.
## Input types
### Required
- **`audio`**
    - The audio data to be visualized. It is crucial for understanding the audio's structure and for generating its visual representation.
    - Comfy dtype: `AUDIO`
    - Python dtype: `np.ndarray`
- **`frame_rate`**
    - Defines the number of frames per second for the visualization, affecting the smoothness and detail of the output.
    - Comfy dtype: `INT`
    - Python dtype: `int`
### Optional
- **`start_frame`**
    - Specifies the starting frame for the visualization, allowing for partial visualization of the audio clip.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`end_frame`**
    - Determines the ending frame for the visualization, enabling users to focus on a specific segment of the audio clip.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SaltAudioVisualizer:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "audio": ("AUDIO", {}),
                "frame_rate": ("INT", {"default": 8, "min": 1, "max": 244}),
            },
            "optional": {
                "start_frame": ("INT", {"min": 0, "default": 0}),
                "end_frame": ("INT", {"min": 0, "default": -1}),
            },
        }

    RETURN_TYPES = ()
    RETURN_NAMES = ()
    OUTPUT_NODE = True

    FUNCTION = "visualize_audio"
    CATEGORY = f"{MENU_NAME}/{SUB_MENU_NAME}/Audio/Util"

    def visualize_audio(self, audio, frame_rate, start_frame=0, end_frame=-1):
        TEMP = folder_paths.get_temp_directory()
        os.makedirs(TEMP, exist_ok=True)

        audio_segment = AudioSegment.from_file(io.BytesIO(audio), format="wav", dir=TEMP)
        
        frame_duration_ms = 1000 / frame_rate
        start_ms = start_frame * frame_duration_ms
        end_ms = end_frame * frame_duration_ms if end_frame != -1 else len(audio_segment)
        
        relevant_audio_segment = audio_segment[start_ms:end_ms]

        samples = np.array(relevant_audio_segment.get_array_of_samples())
        if relevant_audio_segment.channels == 2:
            samples = samples.reshape((-1, 2))
            samples = samples.sum(axis=1) / 2

        max_val = max(abs(samples.min()), samples.max())
        normalized_samples = samples / max_val

        total_frames = len(normalized_samples) / (audio_segment.frame_rate / frame_rate)
        frame_numbers = np.linspace(start_frame, start_frame + total_frames, num=len(normalized_samples), endpoint=False)

        plt.figure(figsize=(10, 4))
        plt.plot(frame_numbers, normalized_samples, linewidth=0.5)
        plt.title("Audio Visualization")
        plt.ylim(-1, 1) 
        plt.xlabel("Frame")
        plt.ylabel("Amplitude")
        
        filename = str(uuid.uuid4())+"_visualization.png"
        file_path = os.path.join(TEMP, filename)
        plt.savefig(file_path)
        plt.close()

        ui_output = {
            "ui": {
                "images": [
                    {
                        "filename": filename,
                        "subfolder": "",
                        "type": "temp"
                    }
                ]
            }
        }

        return ui_output
    
    @staticmethod
    def gen_hash(input_dict):
        sorted_json = json.dumps(input_dict, sort_keys=True)
        hash_obj = hashlib.sha256()
        hash_obj.update(sorted_json.encode('utf-8'))
        return hash_obj.hexdigest()
    
    @classmethod
    def IS_CHANGED(cls, **kwargs):
        return cls.gen_hash(kwargs)

```
