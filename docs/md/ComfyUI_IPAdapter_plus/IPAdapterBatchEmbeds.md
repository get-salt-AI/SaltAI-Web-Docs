# IPAdapter Batch Embeds
## Documentation
- Class name: `IPAdapterBatchEmbeds`
- Category: `ipadapter`
- Output node: `False`

This node is responsible for batching embeddings by concatenating two sets of embeddings along a specified dimension. It is useful for combining embeddings from different sources or models before further processing.
## Input types
### Required
- **`embed1`**
    - The first set of embeddings to be concatenated. It plays a crucial role in the batching process as it forms the initial part of the combined embeddings.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `EMBEDS`
- **`embed2`**
    - The second set of embeddings to be concatenated with the first set. It is equally important as it completes the combined embeddings, allowing for more comprehensive data representation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `EMBEDS`
## Output types
- **`embeds`**
    - The concatenated embeddings resulting from combining the two input sets. This output is ready for further processing or analysis.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `EMBEDS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used for combining embeddings from different AI models or sources, the IPAdapterBatchEmbeds node concatenates two sets of embeddings along a specified dimension, preparing them for further processing or analysis.
## Source code
```python
class IPAdapterBatchEmbeds:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "embed1": ("EMBEDS",),
            "embed2": ("EMBEDS",),
        }}

    RETURN_TYPES = ("EMBEDS",)
    FUNCTION = "batch"
    CATEGORY = "ipadapter"

    def batch(self, embed1, embed2):
        return (torch.cat((embed1, embed2), dim=1), )

```
