# Oracle Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltOracleQueryBuilder is an interactive Oracle helper node for schema exploration and lightweight query construction. It can list tables in a schema, inspect table columns, suggest join relationships, and build and execute a basic query from a natural-language requirement description. It emphasizes discoverability and guidance rather than full-featured arbitrary SQL generation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclequerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to explore the structure of an Oracle database and prototype basic queries without writing much SQL. After configuring Oracle credentials upstream, start with action="discover_tables" to list tables in a schema, then use action="get_table_columns" on a chosen table to understand its columns. For relationship planning, use action="suggest_joins" to get example join suggestions (for instance, EMPLOYEES joined to DEPARTMENTS). For quick data retrieval, choose action="build_query" and describe what you need in query_requirements; the node picks a simple predefined JOIN query if it detects certain patterns (like employees and departments) or falls back to a generic sample query, executes it, and returns results. Place SaltOracleQueryBuilder after Oracle credential or connection nodes (such as SaltOracleConnectionString and the Oracle credential template node) and before downstream analytics, export, or visualization nodes. It is commonly used together with SaltOracleListSchemas (schema selection), SaltOracleVisualQuery (visual construction of complex queries), and SaltOracleComplexQuery (for fully custom SQL). A practical workflow is: list schemas → discover tables → get table columns → review suggested joins → use build_query for a first draft → then refine the SQL in Visual or Complex query nodes for production use.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">["discover_tables", "get_table_columns", "suggest_joins", "build_query"]</td><td style="word-wrap: break-word;">Which operation to run. "discover_tables" lists the tables in target_schema. "get_table_columns" retrieves column metadata for table_name. "suggest_joins" returns example join configurations for table_name. "build_query" constructs and executes a simple SQL query based on query_requirements.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle schema to explore. If left empty, the node uses the authenticated user’s default schema. Used by discover_tables to scope table listing and passed as context to column discovery, join suggestions, and query building. Must be a schema accessible to the configured Oracle credentials.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table to inspect or analyze. Required when action is "get_table_columns" or "suggest_joins"; if empty for those actions, the node raises an error. Ignored for "discover_tables" and "build_query". Must correspond to an existing table in target_schema or in the default schema.</td><td style="word-wrap: break-word;">EMPLOYEES</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural-language description of the data you want when using action="build_query". The implementation uses simple keyword checks (for example, detecting both "employee" and "department") to choose between a predefined employees–departments JOIN query and a generic sample query such as selecting a few rows from EMPLOYEES. It is great for quick demos and exploration but not for complex business logic.</td><td style="word-wrap: break-word;">I want a list of employees with their department names so I can review staffing by department.</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">["text", "html", "xlsx", "pdf", "all"]</td><td style="word-wrap: break-word;">Controls how the node formats its response. "text" returns a textual summary along with JSON data. "html" produces an HTML table representation. "xlsx" generates an Excel-style export. "pdf" produces a PDF report. "all" returns a combined response with multiple export-ready formats in the same result object.</td><td style="word-wrap: break-word;">all</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">DICT</td><td style="word-wrap: break-word;">A dictionary containing the operation results and any formatted exports. For "discover_tables" it includes a list of tables in the schema; for "get_table_columns" it contains column metadata for the specified table; for "suggest_joins" it holds a suggestions structure with recommended joins; and for "build_query" it contains the query results plus the auto-generated SQL and the original requirements. When output_format is "all", additional keys may hold HTML, XLSX, and PDF representations or references.</td><td style="word-wrap: break-word;">{"title": "Oracle Join Suggestions: EMPLOYEES", "suggestions": {"table": "EMPLOYEES", "suggested_joins": [{"type": "INNER", "table": "DEPARTMENTS", "schema": "HR", "on": "EMPLOYEES.DEPARTMENT_ID = DEPARTMENTS.DEPARTMENT_ID", "description": "Join with departments table to get department information"}]}, "format": "text"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Metadata-heavy actions like discover_tables and get_table_columns can be slow on large schemas; narrow target_schema and avoid unnecessary repeated calls for better performance.
- **Limitations**: The build_query action uses simple keyword heuristics and a small set of predefined SQL patterns; it is not a general natural-language-to-SQL engine and should not be used for complex or safety-critical queries.
- **Behavior**: When action is "get_table_columns" or "suggest_joins" and table_name is empty, the node raises a validation error instead of returning an empty structure, which will stop the pipeline until corrected.
- **Behavior**: The suggest_joins implementation returns canned join suggestions (for example, EMPLOYEES → DEPARTMENTS) rather than analyzing real foreign-key relationships; always review and adapt the suggested joins for your schema.
- **Format Handling**: With output_format set to "all", the result may contain multiple file-like exports (HTML, Excel, PDF) in addition to JSON; ensure downstream nodes are prepared to handle these richer payloads.

## Troubleshooting
- **Error about missing table name**: If you see an error such as "Table name is required for column discovery" or "Table name is required for join suggestions", make sure table_name is set whenever action is "get_table_columns" or "suggest_joins".
- **Unknown action error**: An error like "Unknown action: X" means the action value is not supported. Set action to one of: "discover_tables", "get_table_columns", "suggest_joins", or "build_query".
- **Empty tables or columns output**: If discover_tables or get_table_columns returns no data, verify that target_schema is correct and that the Oracle user in your credentials has privileges to see that schema and its tables.
- **Built query not matching expectations**: If build_query returns a generic query or data that does not match your intent, rewrite query_requirements to clearly name the entities and relationships (for example, mention both "employees" and "departments"), or switch to SaltOracleVisualQuery or SaltOracleComplexQuery for full SQL control.
