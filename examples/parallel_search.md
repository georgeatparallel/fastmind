# Parallel Search MCP example

This example connects to the anonymous [Parallel Search MCP](https://docs.parallel.ai/integrations/mcp/search-mcp)
using Streamable HTTP. It discovers `web_search` and `web_fetch`, preserves their
input schemas, and registers them with `FastMind.register_tool`. A FastMind event
runs a request → `ToolNode` → response graph and prints the MCP result, including
source URLs and excerpts. The routing is deterministic; no LLM is used.

From a clean checkout, install the optional dependencies in a virtual environment
(Python 3.10 or later). Run the module from the directory containing the `fastmind`
checkout, as with the other FastMind examples:

```bash
git clone https://github.com/kandada/fastmind.git
python -m venv .venv
source .venv/bin/activate
pip install -r fastmind/examples/requirements-mcp.txt
python -m fastmind.examples.parallel_search "Python asyncio task cancellation"
python -m fastmind.examples.parallel_search "Explain task cancellation" --url https://docs.python.org/3/library/asyncio-task.html
```

No Parallel API key, LLM key, or `.env` file is needed. Anonymous access is free
for light use and subject to server rate limits. Model inference would be a
separate dependency and cost if you adapt this to an LLM agent. The example sends
`FastMind/<version> ParallelSearchExample` as its User-Agent and reuses one random
conversation ID for discovery-backed tool execution. Calls use a 30-second HTTP
timeout, a 60-second MCP response timeout, and a 90-second overall CLI timeout;
errors cause a nonzero exit. It does not retry rate-limit errors.

To adapt the discovered tools to your own graph, keep the MCP session open for
the entire lifetime of the `ToolNode`. `create_app` demonstrates registration
before constructing the node, and `app.get_tool_schemas()` exposes the live
OpenAI-compatible schemas. `make_tool` supplies the stable conversation ID on
each call and preserves the full MCP response as JSON in `tool_results`. Existing
tools and examples are unaffected because these dependencies and registration
are opt-in.
