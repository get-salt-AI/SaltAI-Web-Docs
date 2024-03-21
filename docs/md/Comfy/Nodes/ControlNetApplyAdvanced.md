# Apply ControlNet (Advanced)
## Documentation
- Class name: `ControlNetApplyAdvanced`
- Category: `conditioning`
- Output node: `False`

This node applies advanced control net transformations to a given image, based on the provided conditioning, control net, and strength parameters. It supports additional customization through start and end percentages, allowing for more precise control over the application of the control net. The method enhances or modifies the conditioning of the image by applying control hints derived from the image itself, adjusted by the strength parameter, and further refined by the specified percentage range.
## Input types
### Required
- **`positive`**
    - The positive conditioning to be applied or enhanced on the image. It plays a crucial role in determining the final output by providing a base set of conditions that the control net aims to amplify or modify.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - The negative conditioning, serving as a counterbalance to the positive conditioning. It helps in fine-tuning the final output by providing conditions that the control net should suppress or negate.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`control_net`**
    - The control net model used to apply transformations to the image. It is a crucial component that determines how the conditioning is modified or enhanced.
    - Python dtype: `ControlNet`
    - Comfy dtype: `CONTROL_NET`
- **`image`**
    - The target image to which the control net transformations are applied. The image serves as a basis for generating control hints that guide the conditioning modification.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`strength`**
    - Determines the intensity of the control net application. A higher strength value results in more pronounced modifications to the conditioning.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`start_percent`**
    - Specifies the starting percentage of the control net application, allowing for targeted modifications within a specific range of the image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_percent`**
    - Defines the ending percentage of the control net application, complementing the start_percent to confine the control net's effects within a precise segment of the image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The adjusted negative conditioning post control net application, showcasing the suppression or negation of certain conditions.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,KSamplerAdvanced,ControlNetApplyAdvanced,KSampler //Inspire,UltimateSDUpscale,ToBasicPipe,FaceDetailer,Reroute,SamplerCustom,Bus Node`


## Source code
```python
class ControlNetApplyAdvanced:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"positive": ("CONDITIONING", ),
                             "negative": ("CONDITIONING", ),
                             "control_net": ("CONTROL_NET", ),
                             "image": ("IMAGE", ),
                             "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                             "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                             "end_percent": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001})
                             }}

    RETURN_TYPES = ("CONDITIONING","CONDITIONING")
    RETURN_NAMES = ("positive", "negative")
    FUNCTION = "apply_controlnet"

    CATEGORY = "conditioning"

    def apply_controlnet(self, positive, negative, control_net, image, strength, start_percent, end_percent):
        if strength == 0:
            return (positive, negative)

        control_hint = image.movedim(-1,1)
        cnets = {}

        out = []
        for conditioning in [positive, negative]:
            c = []
            for t in conditioning:
                d = t[1].copy()

                prev_cnet = d.get('control', None)
                if prev_cnet in cnets:
                    c_net = cnets[prev_cnet]
                else:
                    c_net = control_net.copy().set_cond_hint(control_hint, strength, (start_percent, end_percent))
                    c_net.set_previous_controlnet(prev_cnet)
                    cnets[prev_cnet] = c_net

                d['control'] = c_net
                d['control_apply_to_uncond'] = False
                n = [t[0], d]
                c.append(n)
            out.append(c)
        return (out[0], out[1])

```
