# Document Node
## Documentation
- Class name: `DocumentNode`
- Category: `LLM Nodes`
- Output node: `True`

This node is responsible for generating documentation for a specific node within a pack. It identifies the node based on its display name and pack name, then retrieves and formats its documentation.
## Input types
### Required
- **`name`**
    - The unique identifier for the node, composed of the pack name and node display name. It is used to locate the specific node within a pack for documentation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`chunk_lines`**
    - Specifies the number of lines each documentation chunk should contain. This helps in managing the size of the documentation generated.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`chunk_lines_overlap`**
    - Determines the number of lines that can overlap between two consecutive chunks of documentation. This ensures continuity and context preservation in the documentation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`max_chars`**
    - Sets the maximum number of characters allowed in the documentation. This limit helps in keeping the documentation concise.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`num_retries`**
    - Defines the number of attempts to regenerate the documentation in case of failure. This ensures reliability in documentation generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`top_k`**
    - Determines the top K models to consider for generating the documentation. This parameter helps in selecting the best model based on performance.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`regenerate`**
    - Controls whether to regenerate the documentation, offering flexibility based on the requirement. This choice influences the documentation generation process, potentially leading to multiple attempts for optimal results.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`model`**
    - Specifies the model to be used for generating the documentation. This parameter allows for flexibility in choosing the appropriate model, directly impacting the quality and style of the generated documentation.
    - Python dtype: `str`
    - Comfy dtype: `MODEL`
## Output types
- **`string`**
    - The generated documentation for the specified node. This output is a string that contains the formatted documentation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The Document Node is primarily utilized for generating and formatting documentation for a specific node within a pack, based on its display name and pack name. It takes the unique identifier of the node, along with parameters like chunk lines, overlap, and model choice, to produce a concise, formatted documentation string as output.
## Source code
```python
class DocumentNode(DocumentPack):
    @classmethod
    def get_all_names(cls):
        cls.all_packs = get_all_nodes_packs()
        return [
            f"{pack_name}/{node['display_name']}"
            for pack_name, pack in cls.all_packs.items()
            for node in pack["nodes"].values()
        ]

    def document(self, *args, **kwargs) -> Tuple:
        name = kwargs.pop("name")
        pack_name = name.split("/")[0]
        node_display_name = "/".join(name.split("/")[1:])
        for node_name, node_info in self.all_packs[pack_name]["nodes"].items():
            if node_info["display_name"] == node_display_name:
                break
        nodes_list = [node_name]
        return self._document(pack_name=pack_name, nodes_list=nodes_list, *args, **kwargs)

```
