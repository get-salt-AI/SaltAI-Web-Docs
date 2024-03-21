# Load IPAdapter Embeds
## Documentation
- Class name: `IPAdapterLoadEmbeds`
- Category: `ipadapter`
- Output node: `False`

The `IPAdapterLoadEmbeds` node is designed to load embedding vectors saved in a specific format (.ipadpt) from a designated directory. It facilitates the retrieval of pre-processed embeddings, which can be utilized in subsequent operations or models requiring such embeddings.
## Input types
### Required
- **`embeds`**
    - Specifies the file name of the embedding to be loaded. The selection is limited to files with the '.ipadpt' extension within the input directory, ensuring that only compatible embeddings are processed.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`embeds`**
    - Returns the loaded embedding tensor, making it available for further processing or analysis.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `EMBEDS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The `IPAdapterLoadEmbeds` node is essential for loading pre-processed embedding vectors (.ipadpt format) from a specified directory, making these embeddings available for further processing or analysis in subsequent nodes. It accepts a directory containing `.ipadpt` files as input and outputs the corresponding embedding tensors.
## Source code
```python
class IPAdapterLoadEmbeds:
    @classmethod
    def INPUT_TYPES(s):
        input_dir = folder_paths.get_input_directory()
        files = [os.path.relpath(os.path.join(root, file), input_dir) for root, dirs, files in os.walk(input_dir) for file in files if file.endswith('.ipadpt')]
        return {"required": {"embeds": [sorted(files), ]}, }

    RETURN_TYPES = ("EMBEDS", )
    FUNCTION = "load"
    CATEGORY = "ipadapter"

    def load(self, embeds):
        path = folder_paths.get_annotated_filepath(embeds)
        output = torch.load(path).cpu()

        return (output, )

```
