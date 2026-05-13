---
name: LangGraph Agent
description: 'Use when building stateful AI agent workflows using LangGraph and LangChain. Use this skill whenever the user mentions LangGraph, LangChain, agent graphs, stateful agents, tool-calling loops, multi-agent orchestration, agentic workflows, conditional routing, graph-based AI, ReAct agents, or wants to build an AI agent with Python that has tools, memory, or multi-step reasoning. Also trigger when you see imports from langgraph, langchain, or langchain_core in existing code, or when the user wants '
icon: icon.svg
metadata:
  category: dev/code
  family: design
  lifecycle: active
  canonical_slug: langgraph-agent
  icon_style: craft-category-glyph-v1
---

# LangGraph Agent Development

Build production-grade AI agent workflows using LangGraph (graph orchestration) + LangChain (LLM abstraction).

## When to Use What

| Need | Use | Not |
|------|-----|-----|
| Stateful multi-step agent with tools | LangGraph `StateGraph` | Raw LangChain chains |
| Simple prompt → response | LangChain `ChatModel.invoke()` | LangGraph (overkill) |
| Conditional routing between steps | `add_conditional_edges()` | Manual if/else loops |
| Tool calling with automatic loop | `ToolNode` + `bind_tools` | Manual tool dispatch |
| Durable/crash-safe workflows | LangGraph checkpointers | In-memory state only |
| Multi-agent orchestration | Supervisor/hierarchical patterns | Single monolithic agent |

## Core Architecture

```
START → [agent_node] → should_continue?
                          ├── has tool_calls → [tool_node] → [post_tool] → agent_node (loop)
                          └── no tool_calls → END
```

This is the **ReAct loop** — the agent reasons, calls tools, observes results, and repeats until it has a final answer.

## Installation

```bash
pip install langgraph langchain langchain-community langchain-ollama
# Optional cloud LLMs:
pip install langchain-anthropic langchain-openai
```

**Python 3.10+** required. LangGraph 1.x is current (as of 2026).

## 1. State Definition

State is a `TypedDict` that flows through every node. Use `add_messages` reducer for conversation history:

```python
from typing import Annotated, Literal, TypedDict
from langchain_core.messages import BaseMessage
from langgraph.graph.message import add_messages

class AgentState(TypedDict):
    messages: Annotated[list[BaseMessage], add_messages]  # append semantics
    # Add domain-specific fields:
    mode: str
    retrieved_context: list[dict]
```

The `add_messages` annotation means new messages get **appended**, not replaced. This is critical — without it, each node would overwrite the entire message list.

## 2. Tool Definition

Use the `@tool` decorator from `langchain_core.tools`:

```python
from langchain_core.tools import tool

@tool
def search_docs(query: str, top_k: int = 5) -> list[dict]:
    """Search the knowledge base for relevant documents.

    Args:
        query: Natural language search query.
        top_k: Number of results to return.

    Returns:
        List of matching documents with content and metadata.
    """
    # Your implementation here
    return results
```

**Docstring quality matters** — the LLM reads it to decide when/how to call the tool. Include clear Args/Returns, usage context, and parameter constraints.

## 3. Graph Construction

```python
from langgraph.graph import END, START, StateGraph
from langgraph.prebuilt import ToolNode

ALL_TOOLS = [search_docs, other_tool]

def agent_node(state: AgentState) -> dict:
    llm = ChatOllama(model="qwen3:8b", base_url="http://localhost:11434")
    llm_with_tools = llm.bind_tools(ALL_TOOLS)
    messages = [SystemMessage(content="You are...")] + list(state["messages"])
    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

def should_continue(state: AgentState) -> str:
    last = state["messages"][-1]
    if hasattr(last, "tool_calls") and last.tool_calls:
        return "tools"
    return END

# Build the graph
graph = StateGraph(AgentState)
graph.add_node("agent", agent_node)
graph.add_node("tools", ToolNode(ALL_TOOLS))
graph.add_edge(START, "agent")
graph.add_conditional_edges("agent", should_continue, {"tools": "tools", END: END})
graph.add_edge("tools", "agent")  # loop back after tool execution
compiled = graph.compile()
```

## 4. Invoking the Graph

```python
from langchain_core.messages import HumanMessage

# Single query
result = compiled.invoke({
    "messages": [HumanMessage(content="What is SASG K11-03A?")],
    "mode": "general",
    "retrieved_context": [],
})
print(result["messages"][-1].content)

# Streaming
for step in compiled.stream({"messages": [HumanMessage(content="...")]}, stream_mode="updates"):
    print(step)
```

## 5. Hybrid LLM Routing (Local + Cloud)

```python
from langchain_ollama import ChatOllama
from langchain_anthropic import ChatAnthropic  # pip install langchain-anthropic

def get_llm(tier: str = "local"):
    if tier == "cloud":
        return ChatAnthropic(model="Codex-sonnet-4-20250514")
    return ChatOllama(model="qwen3:8b", base_url="http://localhost:11434", num_ctx=8192)
```

**When to escalate to cloud**: local model fails validation, multi-step chains >3 hops, user requests it, or structured output extraction fails.

## 6. Post-Tool Processing

Add a node after tools to extract state changes from tool results:

```python
def post_tool(state: AgentState) -> dict:
    updates = {}
    for msg in reversed(state["messages"]):
        if hasattr(msg, "name") and msg.name == "set_mode":
            # Parse mode from tool result, update state
            updates["mode"] = extract_mode(msg.content)
            break
    return updates

graph.add_node("post_tool", post_tool)
graph.add_edge("tools", "post_tool")
graph.add_edge("post_tool", "agent")
```

## 7. Persistence / Checkpointing

```python
from langgraph.checkpoint.memory import MemorySaver
# Or for production: from langgraph.checkpoint.sqlite import SqliteSaver

checkpointer = MemorySaver()
compiled = graph.compile(checkpointer=checkpointer)

# Resume conversations with thread_id
config = {"configurable": {"thread_id": "user-123"}}
result = compiled.invoke({"messages": [HumanMessage(content="...")]}, config)
# Later, same thread_id picks up where it left off
```

## 8. Wrapping Existing Code as Tools

When integrating with an existing codebase (e.g., a RAG pipeline), import and wrap — don't rewrite:

```python
import sys
sys.path.insert(0, str(Path(__file__).parent.parent / "existing-framework"))
from vectorstore import init_db, search
from embeddings import embed_single

_conn = None
def _get_conn():
    global _conn
    if _conn is None:
        _conn = init_db()
    return _conn

@tool
def rag_search(query: str, top_k: int = 8) -> list[dict]:
    """Search the knowledge base."""
    conn = _get_conn()
    embedding = embed_single(query)
    return search(conn, embedding, top_k=top_k)
```

## Common Gotchas

1. **sqlite-vec / sqlite_vec**: If wrapping a vectorstore that uses sqlite-vec, install it in your venv too
2. **Python 3.14 + Pydantic V1**: LangChain warns about Pydantic V1 compat — it works but noisy
3. **`bind_tools` requires tool list**: Pass the same list to both `bind_tools` and `ToolNode`
4. **System messages**: Prepend to `state["messages"]` in the agent node, don't store in state
5. **Streaming qwen3 `<think>` blocks**: Filter `<think>...</think>` from output when using qwen3

## Advanced Patterns

For multi-agent, parallelization, orchestrator-worker, and FastAPI serving patterns, see `references/advanced-patterns.md`.
