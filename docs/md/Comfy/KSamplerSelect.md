# KSamplerSelect
## Documentation
- Class name: `KSamplerSelect`
- Category: `sampling/custom_sampling/samplers`
- Output node: `False`

The `KSamplerSelect` node is designed to select a specific sampler object based on the provided sampler name. It facilitates the dynamic selection of sampling strategies within the broader sampling framework, allowing for flexible experimentation and optimization of sampling processes.
## Input types
### Required
- **`sampler_name`**
    - Specifies the name of the sampler to be selected. This parameter enables the dynamic selection of different sampling strategies, allowing users to tailor the sampling process to specific requirements or preferences.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`sampler`**
    - Returns the selected sampler object, which can be used for further sampling operations. This enables the dynamic and flexible use of different sampling strategies within the application.
    - Python dtype: `comfy.samplers.Sampler`
    - Comfy dtype: `SAMPLER`
## Usage tips
- Infra type: `CPU`
- Common nodes: `SamplerCustom,Reroute`


## Source code
```python
class KSamplerSelect:
    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"sampler_name": (comfy.samplers.SAMPLER_NAMES, ),
                      }
               }
    RETURN_TYPES = ("SAMPLER",)
    CATEGORY = "sampling/custom_sampling/samplers"

    FUNCTION = "get_sampler"

    def get_sampler(self, sampler_name):
        sampler = comfy.samplers.sampler_object(sampler_name)
        return (sampler, )

```
