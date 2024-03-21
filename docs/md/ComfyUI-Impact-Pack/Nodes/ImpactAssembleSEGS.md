# Assemble (SEGS)
## Documentation
- Class name: `ImpactAssembleSEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

The `ImpactAssembleSEGS` node assembles a SEGS data structure from provided segment header and segment element inputs. This operation is crucial for constructing a complete SEGS object, which is a fundamental data structure used for representing segmented images or regions within the ImpactPack.
## Input types
### Required
- **`seg_header`**
    - The segment header provides essential metadata for the SEGS structure, such as dimensions and properties of the segmented image or region.
    - Python dtype: `Tuple[str]`
    - Comfy dtype: `SEGS_HEADER`
- **`seg_elt`**
    - The segment element represents a specific segmented part or region within the overall SEGS structure. It is a key component in assembling the complete SEGS object.
    - Python dtype: `Tuple[SEG_ELT]`
    - Comfy dtype: `SEG_ELT`
## Output types
- **`segs`**
    - The assembled SEGS object, representing a complete segmented image or region with its corresponding metadata and elements.
    - Python dtype: `Tuple[Tuple[str], List[SEG_ELT]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class AssembleSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "seg_header": ("SEGS_HEADER", ),
                     "seg_elt": ("SEG_ELT", ),
                     },
                }

    INPUT_IS_LIST = True

    RETURN_TYPES = ("SEGS", )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, seg_header, seg_elt):
        return ((seg_header[0], seg_elt), )

```
