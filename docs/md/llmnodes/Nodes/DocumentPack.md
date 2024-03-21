# Document Pack
## Documentation
- Class name: `DocumentPack`
- Category: `LLM Nodes`
- Output node: `True`

The `DocumentPack` class serves as a foundational component in a system designed to manage and document nodes within a larger framework. It primarily acts as a base class, providing essential functionalities and structures for node documentation and management. This includes methods for retrieving all node names and packs, which are crucial for organizing and accessing the various nodes available within the system.
## Input types
### Required
- **`name`**
    - The `name` parameter specifies the unique identifier of a node within a pack. It is essential for retrieving specific node documentation, as it determines which node's information is to be documented based on its unique name.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`chunk_lines`**
    - Specifies the number of lines each code chunk should contain. This is important for controlling the granularity of documentation and affects how code is segmented and documented.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`chunk_lines_overlap`**
    - Determines the number of lines that consecutive code chunks will overlap. This parameter is crucial for ensuring continuity and context between chunks, enhancing the quality of generated documentation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`max_chars`**
    - Sets the maximum number of characters allowed in a single code chunk. This parameter helps in managing the size of documentation chunks, ensuring they are of a manageable size for processing and understanding.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`num_retries`**
    - Indicates the number of times the documentation generation process should retry in case of failures. This parameter is important for robustness, ensuring that temporary issues do not prevent documentation from being generated.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`top_k`**
    - Specifies the number of top results to consider when generating documentation. This parameter influences the quality and relevance of the generated documentation by focusing on the most promising results.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`regenerate`**
    - Controls whether existing documentation should be regenerated. This parameter allows for flexibility in managing documentation updates and ensures that the most current information is always available.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`model`**
    - Refers to the model used for generating documentation. This parameter is critical as it determines the quality and style of the generated documentation, based on the capabilities of the specified model.
    - Python dtype: `Dict[str, Any]`
    - Comfy dtype: `MODEL`
## Output types
- **`string`**
    - The output is a tuple containing the generated documentation for the specified nodes. This provides a structured and accessible way to retrieve documentation for nodes within the system.
    - Python dtype: `Tuple[str,]`
    - Comfy dtype: `STRING`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The DocumentPack node is primarily used for generating and managing documentation for other nodes within a pipeline, taking as input parameters like node names, code chunk specifications, and model details, and outputs structured documentation in the form of strings. This node is essential for organizing and accessing comprehensive documentation on the various nodes available within the system, ensuring users can easily understand and manage their AI pipelines.
## Source code
```python
class DocumentPack:
    @classmethod
    def get_all_names(cls):
        cls.all_packs = get_all_nodes_packs()
        return [f"{pack_name}/{len(pack['nodes'])} nodes" for pack_name, pack in cls.all_packs.items()]

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
                "regenerate": ([e.value for e in RegenerateOptions], {"default": RegenerateOptions.no.value}),
                "model": ("MODEL",),
            }
        }

    RETURN_TYPES = ("STRING",)
    OUTPUT_NODE = True
    FUNCTION = "document"
    CATEGORY = NAME

    def document(self, *args, **kwargs) -> Tuple:
        pack_name = kwargs.pop("name").split("/")[0]
        nodes_list = self.all_packs[pack_name]["nodes"]
        return self._document(pack_name=pack_name, nodes_list=nodes_list, *args, **kwargs)

    def _document(
        self,
        pack_name: str,
        nodes_list: Dict[str, Any],
        chunk_lines: int,
        chunk_lines_overlap: int,
        max_chars: int,
        num_retries: int,
        top_k: int,
        regenerate: str,
        model: Dict[str, Any],
    ) -> Tuple[str,]:
        self._init_phoenix()
        emb_name = model["name"].split("_")[1] + f"_{chunk_lines}_{chunk_lines_overlap}_{max_chars}"
        query_name = model["name"] + f"_{chunk_lines}_{chunk_lines_overlap}_{max_chars}_{num_retries}_{top_k}"
        Settings.llm = model["llm"]
        Settings.embed_model = model["embed_model"]
        load_index_kwargs = dict(
            emb_name=emb_name,
            chunk_lines=chunk_lines,
            chunk_lines_overlap=chunk_lines_overlap,
            max_chars=max_chars,
            regenerate=regenerate == "index & doc",
        )
        # adding comfy files because many packs import from there
        global comfy_nodes_index
        if comfy_nodes_index is None:
            comfy_nodes_index = self._load_index(pack_name="Comfy", **load_index_kwargs)
        indexes = [comfy_nodes_index]
        if pack_name != "Comfy":
            indexes.append(self._load_index(pack_name=pack_name, **load_index_kwargs))
        node_query_engine = NodeQueryEngine(indexes=indexes, num_retries=num_retries, top_k=top_k)
        is_max, no_skip, no_error = [], [], []
        for node_name in nodes_list:
            debug_info = self._document_node(node_query_engine, query_name, pack_name, node_name, regenerate)
            score, max_score = debug_info["_eval_score"].split("/")
            is_max.append(int(score) == int(max_score))
            no_skip.append(int(not debug_info["_has_unknown"]))
            no_error.append(debug_info["_error"] is None)
        Settings.llm = "default"
        Settings.embed_model = "default"
        log_str = f"""Nodes with max score: {sum(is_max)}/{len(is_max)}\n
Nodes without skipped fields: {sum(no_skip)}/{len(no_skip)}\n
Nodes without errors: {sum(no_error)}/{len(no_error)}"""
        self._log_phoenix()
        return (log_str,)

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
            logger.debug(f"Loading index for {pack_name} from {persist_dir}...")
            return load_index_from_storage(StorageContext.from_defaults(persist_dir=persist_dir))
        persist_dir.mkdir(parents=True, exist_ok=True)
        code_path = self.all_packs[pack_name]["code_path"]
        code_files = [code_path] if str(code_path).endswith(".py") else list(code_path.rglob("*.py"))
        documents = SimpleDirectoryReader(input_files=code_files).load_data()
        splitter = CodeSplitter(
            language="python", chunk_lines=chunk_lines, chunk_lines_overlap=chunk_lines_overlap, max_chars=max_chars
        )
        index = VectorStoreIndex(splitter.get_nodes_from_documents(documents), use_async=True)
        index.storage_context.persist(persist_dir=persist_dir)
        return index

    def _document_node(
        self,
        node_query_engine: NodeQueryEngine,
        query_name: str,
        pack_name: str,
        node_name: str,
        regenerate: str,
    ) -> Dict[str, str]:
        json_file = MAIN_CACHE / "documented_nodes" / query_name / pack_name / f"{node_name}.json"
        old_score, old_unknown = None, None
        if regenerate in ["no", "failed"] and json_file.exists():
            with open(json_file, "r") as file:
                old_debug_info = json.load(file)["_debug_info"]
                if regenerate == "no":
                    logger.debug(f"Loading existing response for {node_name} from {json_file}...")
                    return old_debug_info
                if regenerate == "failed":
                    if old_debug_info["_error"] is None:
                        score, max_score = old_debug_info["_eval_score"].split("/")
                        old_score, old_unknown = int(score) / int(max_score), old_debug_info["_has_unknown"]
                        if not old_unknown and old_score == 1:
                            logger.debug(f"Loading existing response for {node_name} from {json_file}...")
                            return old_debug_info
        node_info = self.all_packs[pack_name]["nodes"][node_name]
        response = node_query_engine.query(node_name, node_info)
        debug_info = response["_debug_info"]
        if old_score is not None:
            # don't update the result if we made it even worse
            score, max_score = debug_info["_eval_score"].split("/")
            new_score, new_unknown = int(score) / int(max_score), debug_info["_has_unknown"]
            if old_unknown:
                if new_unknown and new_score < old_score:
                    return old_debug_info
            else:
                if new_unknown or not new_unknown and new_score < old_score:
                    return old_debug_info

        json_file.parent.mkdir(parents=True, exist_ok=True)
        with open(json_file, "w") as file:
            json.dump(response, file, indent=4)
            return debug_info

    def _init_phoenix(self) -> None:
        if type(sys.stdout).__name__ == "ComfyUIManagerLogger":
            sys.stdout.close_log()
            config = {
                "handlers": [
                    {"sink": sys.stdout, "format": "{time} - {message}"},
                    {"sink": sys.stderr, "format": "{time} - {message}"},
                ],
            }
            logger.configure(**config)
        sess = px.active_session()
        # clear before next run, instead of at the end, to allow debugging of outputs
        if sess is not None:
            sess.end()
        px.launch_app()

    def _log_phoenix(self) -> None:
        from phoenix.trace.dsl import SpanQuery

        query = SpanQuery().select(tokens_in="llm.token_count.prompt", tokens_out="llm.token_count.completion")
        # The Phoenix Client can take this query and return the dataframe
        info_df = px.Client().query_spans(query)
        if info_df is not None:
            logger.info(f"Total tokens in: {info_df.tokens_in.sum()}. Total tokens out: {info_df.tokens_out.sum()}")

```
