# CLIP Text Encode Sequence (v2)
## Documentation
- Class name: `CLIPTextEncodeSequence2`
- Category: `conditioning`
- Output node: `False`

This node processes a sequence of text lines for conditioning, using either a basic or an advanced CLIP text encoding method based on configuration. It supports token normalization and weight interpretation for the advanced encoding method, and organizes the encoded data along with their indices for further processing.
## Input types
### Required
- **`clip`**
    - The CLIP model used for text encoding. It's crucial for converting text into a format that can be used for conditioning in generative models.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
- **`token_normalization`**
    - Indicates whether token normalization is applied during encoding, affecting the representation of the text in the conditioning.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`weight_interpretation`**
    - Determines how weights are interpreted in the advanced encoding process, influencing the final conditioning output.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`cond_keyframes_type`**
    - Defines the method used to distribute keyframes across the conditioning sequence, influencing the temporal structure of the generated content. The choice of method (linear, sinusoidal, etc.) affects how the narrative or visual elements are paced over time, providing a framework for dynamic storytelling or visual transitions.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`frame_count`**
    - Specifies the total number of frames to be generated, directly affecting the length and coverage of the conditioning sequence.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`text`**
    - The input text, potentially containing multiple lines, each intended for separate encoding. The structure and content of this text directly influence the generated conditionings.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`conditioning`**
    - A list of conditionings prepared for further generative processes.
    - Python dtype: `List[Tuple[int, dict]]`
    - Comfy dtype: `CONDITIONING`
- **`int`**
    - The total number of frames in the conditioning sequence, confirming the sequence length.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The CLIPTextEncodeSequence2 node is primarily utilized for processing a sequence of text lines through either basic or advanced CLIP text encoding methods, tailored for dynamic storytelling or visual transitions in AI-generated content. It accepts multiline text as input and outputs a list of conditionings along with their indices and the total frame count, making it ideal for applications requiring nuanced text interpretation and temporal structuring in generative models.
## Source code
```python
class CLIPTextEncodeSequence2:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "clip": ("CLIP", ),
                "token_normalization": (["none", "mean", "length", "length+mean"],),
                "weight_interpretation": (["comfy", "A1111", "compel", "comfy++"],),
                "cond_keyframes_type": (["linear", "sinus", "sinus_inverted", "half_sinus", "half_sinus_inverted"],),
                "frame_count": ("INT", {"default": 100, "min": 1, "max": 1024, "step": 1}),
                "text": ("STRING", {"multiline": True, "default": '''A portrait of a rosebud
A portrait of a blooming rosebud
A portrait of a blooming rose
A portrait of a rose'''}),
            }
        }
        
    RETURN_TYPES = ("CONDITIONING", "INT", "INT")
    RETURN_NAMES = ("conditioning_sequence", "cond_keyframes", "frame_count")
    IS_LIST_OUTPUT = (True, True, False)

    FUNCTION = "encode"
    CATEGORY = "conditioning"

    def encode(self, clip, text, cond_keyframes_type, frame_count, token_normalization, weight_interpretation):
        text = text.strip()
        conditionings = []
        for line in text.splitlines():
            if USE_BLK:
                encoded = blk_adv.encode(clip=clip, text=line, token_normalization=token_normalization, weight_interpretation=weight_interpretation)
            else:
                encoded = CLIPTextEncode.encode(clip=clip, text=line)

            conditionings.append([encoded[0][0][0], encoded[0][0][1]])

        conditioning_count = len(conditionings)
        cond_keyframes = self.calculate_cond_keyframes(cond_keyframes_type, frame_count, conditioning_count)

        return (conditionings, cond_keyframes, frame_count)

    def calculate_cond_keyframes(self, type, frame_count, conditioning_count):
        if type == "linear":
            return np.linspace(frame_count // conditioning_count, frame_count, conditioning_count, dtype=int).tolist()

        elif type == "sinus":
            # Create a sinusoidal distribution
            t = np.linspace(0, np.pi, conditioning_count)
            sinus_values = np.sin(t) 
            # Normalize the sinusoidal values to 0-1 range
            normalized_values = (sinus_values - sinus_values.min()) / (sinus_values.max() - sinus_values.min())
            # Scale to frame count and shift to avoid starting at frame 0
            scaled_values = normalized_values * (frame_count - 1) + 1
            # Ensure unique keyframes by rounding and converting to integer
            unique_keyframes = np.round(scaled_values).astype(int)
            # Deduplicate while preserving order
            unique_keyframes = np.unique(unique_keyframes, return_index=True)[1]
            return sorted(unique_keyframes.tolist())
    
        elif type == "sinus_inverted":
            return (np.cos(np.linspace(0, np.pi, conditioning_count)) * (frame_count - 1) + 1).astype(int).tolist()

        elif type == "half_sinus":
            return (np.sin(np.linspace(0, np.pi / 2, conditioning_count)) * (frame_count - 1) + 1).astype(int).tolist()

        elif type == "half_sinus_inverted":
            return (np.cos(np.linspace(0, np.pi / 2, conditioning_count)) * (frame_count - 1) + 1).astype(int).tolist()

        else:
            raise ValueError("Unsupported cond_keyframes_type: " + type)

```
