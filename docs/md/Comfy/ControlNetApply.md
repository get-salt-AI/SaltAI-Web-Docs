# Apply ControlNet
## Documentation
- Class name: `ControlNetApply`
- Category: `conditioning`
- Output node: `False`

This node applies a control network to an input image and conditioning data, adjusting the conditioning based on the control network's output. The strength parameter controls the intensity of the control network's effect, allowing for fine-tuning of the applied conditioning.
## Input types
### Required
- **`conditioning`**
    - The conditioning data to be modified by the control network. It's crucial for determining the final output as it guides the control network's adjustments.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`control_net`**
    - The control network model used to adjust the conditioning data based on the input image. It plays a key role in how the conditioning is modified.
    - Python dtype: `ControlNet`
    - Comfy dtype: `CONTROL_NET`
- **`image`**
    - The input image that the control network uses to adjust the conditioning data. It serves as a reference for the control network's modifications.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`strength`**
    - A parameter that controls the intensity of the control network's effect on the conditioning data. It allows for fine-tuning the impact of the control network.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The modified conditioning data after applying the control network. It reflects the adjustments made based on the input image and control network's output.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,Reroute,ConditioningSetArea,UltimateSDUpscale,KSampler Adv. (Efficient),ttN pipeKSampler,YDetailer,ToBasicPipe,ToDetailerPipe,ControlNetApply`


## Source code
```python
class ControlNetApply:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", ),
                             "control_net": ("CONTROL_NET", ),
                             "image": ("IMAGE", ),
                             "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01})
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "apply_controlnet"

    CATEGORY = "conditioning"

    def apply_controlnet(self, conditioning, control_net, image, strength):
        if strength == 0:
            return (conditioning, )

        c = []
        control_hint = image.movedim(-1,1)
        for t in conditioning:
            n = [t[0], t[1].copy()]
            c_net = control_net.copy().set_cond_hint(control_hint, strength)
            if 'control' in t[1]:
                c_net.set_previous_controlnet(t[1]['control'])
            n[1]['control'] = c_net
            n[1]['control_apply_to_uncond'] = True
            c.append(n)
        return (c, )

```
