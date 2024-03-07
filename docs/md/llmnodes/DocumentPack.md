# Document Pack
## Documentation
- Class name: `DocumentPack`
- Category: `LLM Nodes`
- Output node: `True`

The `DocumentPack` node automates the documentation process for a collection of nodes within a specified pack. It leverages a language model to generate comprehensive documentation for each node, ensuring that the documentation is up-to-date and accurately reflects the nodes' functionalities. This process involves indexing node information and customizing chat functions to facilitate effective querying and documentation generation.
## Input types
- **`name`**
    - Specifies the name of the node pack to document. This name is crucial as it determines which collection of nodes will be documented, guiding the documentation process.
    - Python dtype: `str`
    - Comfy dtype: `STRING[COMBO]`
- **`chunk_lines`**
    - Determines the chunk size in lines for processing the documentation. This parameter affects how the documentation is segmented, which can influence the comprehensiveness and readability of the generated documentation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`chunk_lines_overlap`**
    - Sets the number of overlapping lines between chunks. This overlap can help ensure continuity and context preservation in the documentation process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`max_chars`**
    - Defines the maximum number of characters for each documentation chunk. This limit helps manage the size of the documentation segments, ensuring they are concise yet informative.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`num_retries`**
    - Specifies the number of retries for querying the language model. This parameter can enhance the accuracy and quality of the generated documentation by allowing multiple attempts.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`top_k`**
    - Determines the top K responses to consider from the language model queries. This selection process can influence the relevance and quality of the documentation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`regenerate`**
    - Indicates whether to regenerate the documentation. This option allows for updating the documentation to reflect any changes or improvements in the nodes.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`model`**
    - Provides the model configuration for documentation generation. This includes settings for the language and embedding models, which are essential for customizing the documentation process.
    - Python dtype: `dict`
    - Comfy dtype: `MODEL`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Utilized primarily for automating the documentation process of a collection of nodes within a specified pack, ensuring up-to-date and accurate reflections of the nodes' functionalities by leveraging a language model.
## Source code
```python
class DocumentPack(DocumentNode):
    @classmethod
    def get_all_names(cls):
        cls.all_packs = get_all_nodes_packs()
        return [f"{pack_name}/{len(pack['nodes'])} nodes" for pack_name, pack in cls.all_packs.items()]

    def document(
        self,
        name: str,
        chunk_lines: int,
        chunk_lines_overlap: int,
        max_chars: int,
        num_retries: int,
        top_k: int,
        regenerate: bool,
        model: Dict[str, Any],
    ) -> Tuple[str,]:
        pack_name = name.split("/")[0]
        emb_name = model["name"].split("_")[0] + f"_{chunk_lines}_{chunk_lines_overlap}_{max_chars}"
        query_name = model["name"] + f"_{chunk_lines}_{chunk_lines_overlap}_{max_chars}_{num_retries}_{top_k}"
        Settings.llm = model["llm"]
        Settings.embed_model = model["embed_model"]
        default_chat_func = CondenseQuestionChatEngine.chat
        CondenseQuestionChatEngine.chat = custom_chat
        index = self._load_index(emb_name, pack_name, chunk_lines, chunk_lines_overlap, max_chars, regenerate)
        node_query_engine = NodeQueryEngine(index, num_retries, top_k)
        for node_name in self.all_packs[pack_name]["nodes"]:
            self._document_node(node_query_engine, query_name, pack_name, node_name, regenerate)
        Settings.llm = "default"
        Settings.embed_model = "default"
        CondenseQuestionChatEngine.chat = default_chat_func
        return ()

```
