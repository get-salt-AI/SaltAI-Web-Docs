# Document Node
## Documentation
- Class name: `DocumentNode`
- Category: `LLM Nodes`
- Output node: `True`

This node is responsible for generating documentation for other nodes within a system. It retrieves node information, queries for detailed documentation using a query engine, and saves the documentation in a structured JSON format. This process can be regenerated as needed, ensuring up-to-date documentation.
## Input types
- **`node_query_engine`**
    - The engine used to query detailed documentation for nodes. It plays a crucial role in fetching and updating node information based on specific queries.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`query_name`**
    - A unique identifier for the query, used to organize and retrieve generated documentation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`pack_name`**
    - The name of the pack containing the node, used to categorize and access nodes within the system.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`node_name`**
    - The specific name of the node being documented, used to identify and retrieve node information.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`regenerate`**
    - A flag indicating whether to regenerate the documentation, allowing for updates or corrections.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Primarily utilized for generating and updating documentation of other nodes within a pipeline, the DocumentNode retrieves detailed information about nodes, queries for documentation using a specific engine, and outputs the documentation in a structured JSON format. This node is essential for maintaining up-to-date documentation without manual intervention.
## Source code
```python
class DocumentNode:
    @classmethod
    def get_all_names(cls):
        cls.all_packs = get_all_nodes_packs()
        return [
            f"{pack_name}/{node['display_name']}"
            for pack_name, pack in cls.all_packs.items()
            for node in pack["nodes"].values()
        ]

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "name": [
                    sorted(cls.get_all_names()),
                ],
                "chunk_lines": ("INT", {"default": 40}),
                "chunk_lines_overlap": ("INT", {"default": 15}),
                "max_chars": ("INT", {"default": 1500}),
                "num_retries": ("INT", {"default": 5}),
                "top_k": ("INT", {"default": 10}),
                "regenerate": ("BOOLEAN", {"default": False}),
                "model": ("MODEL",),
            }
        }

    RETURN_TYPES = ()
    OUTPUT_NODE = True
    FUNCTION = "document"
    CATEGORY = NAME

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
        node_display_name = "/".join(name.split("/")[1:])
        for node_name, node_info in self.all_packs[pack_name]["nodes"].items():
            if node_info["display_name"] == node_display_name:
                break
        emb_name = model["name"].split("_")[0] + f"_{chunk_lines}_{chunk_lines_overlap}_{max_chars}"
        query_name = model["name"] + f"_{chunk_lines}_{chunk_lines_overlap}_{max_chars}_{num_retries}_{top_k}"
        Settings.llm = model["llm"]
        Settings.embed_model = model["embed_model"]
        default_chat_func = CondenseQuestionChatEngine.chat
        CondenseQuestionChatEngine.chat = custom_chat
        index = self._load_index(emb_name, pack_name, chunk_lines, chunk_lines_overlap, max_chars, regenerate)
        node_query_engine = NodeQueryEngine(index, num_retries, top_k)
        self._document_node(node_query_engine, query_name, pack_name, node_name, regenerate)
        Settings.llm = "default"
        Settings.embed_model = "default"
        CondenseQuestionChatEngine.chat = default_chat_func
        return ()

    def _load_index(
        self,
        emb_name: str,
        pack_name: str,
        chunk_lines: int,
        chunk_lines_overlap: int,
        max_chars: int,
        regenerate: bool,
    ) -> VectorStoreIndex:
        persist_dir = MAIN_CACHE / "indices" / emb_name / pack_name
        if not regenerate and persist_dir.exists() and any(persist_dir.iterdir()):
            return load_index_from_storage(StorageContext.from_defaults(persist_dir=persist_dir))
        persist_dir.mkdir(parents=True, exist_ok=True)
        # adding comfy files because many packs import from there
        code_paths = [self.all_packs[pack_name]["code_path"], self.all_packs["Comfy"]["code_path"]]
        code_files = []
        for code_path in code_paths:
            code_files += [code_path] if str(code_path).endswith(".py") else list(code_path.rglob("*.py"))
        documents = SimpleDirectoryReader(input_files=code_files).load_data()
        splitter = CodeSplitter(
            language="python", chunk_lines=chunk_lines, chunk_lines_overlap=chunk_lines_overlap, max_chars=max_chars
        )
        index = VectorStoreIndex(splitter.get_nodes_from_documents(documents))
        index.storage_context.persist(persist_dir=persist_dir)
        return index

    def _document_node(
        self,
        node_query_engine: NodeQueryEngine,
        query_name: str,
        pack_name: str,
        node_name: str,
        regenerate: bool,
    ) -> None:
        json_file = MAIN_CACHE / "documented_nodes" / query_name / pack_name / f"{node_name}.json"
        if not regenerate and json_file.exists():
            with open(json_file, "r") as file:
                return file.read()
        node_info = self.all_packs[pack_name]["nodes"][node_name]
        response = node_query_engine.query(node_name, node_info)
        json_file.parent.mkdir(parents=True, exist_ok=True)
        with open(json_file, "w") as file:
            json.dump(response, file, indent=4)

```
