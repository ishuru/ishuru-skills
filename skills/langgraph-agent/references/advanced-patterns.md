# LangGraph Advanced Patterns

## Multi-Agent Architectures

### Supervisor Pattern
A supervisor agent routes tasks to specialized worker agents:

```python
from langgraph.graph import StateGraph, START, END

class SupervisorState(TypedDict):
    messages: Annotated[list[BaseMessage], add_messages]
    next_agent: str | None

def supervisor_node(state):
    """Decides which agent handles the current task."""
    llm = get_llm()
    route = llm.with_structured_output(Route)
    decision = route.invoke(state["messages"])
    return {"next_agent": decision.agent}

def route_to_agent(state) -> str:
    return state["next_agent"] or END

graph = StateGraph(SupervisorState)
graph.add_node("supervisor", supervisor_node)
graph.add_node("research_agent", research_agent)
graph.add_node("writing_agent", writing_agent)
graph.add_edge(START, "supervisor")
graph.add_conditional_edges("supervisor", route_to_agent, {
    "research": "research_agent",
    "writing": "writing_agent",
    END: END,
})
graph.add_edge("research_agent", "supervisor")
graph.add_edge("writing_agent", "supervisor")
```

### Orchestrator-Worker (Dynamic Fan-Out)
Use `Send()` to dynamically spawn parallel workers:

```python
from langgraph.types import Send

class WorkerState(TypedDict):
    section: str
    result: str

def orchestrator(state):
    sections = analyze_and_split(state["messages"])
    return [Send("worker", {"section": s}) for s in sections]

def worker(state: WorkerState):
    result = llm.invoke(f"Process: {state['section']}")
    return {"result": result.content}

graph.add_conditional_edges("orchestrator", orchestrator, ["worker"])
```

## Parallelization

Execute independent nodes simultaneously:

```python
graph.add_edge(START, "analyze_structure")
graph.add_edge(START, "analyze_style")
graph.add_edge(START, "analyze_content")
graph.add_edge("analyze_structure", "merge")
graph.add_edge("analyze_style", "merge")
graph.add_edge("analyze_content", "merge")
```

## Streaming Modes

```python
# Stream state updates (see which nodes ran)
for step in graph.stream(input, stream_mode="updates"):
    node_name = list(step.keys())[0]
    print(f"Node {node_name}: {step[node_name]}")

# Stream individual values
for step in graph.stream(input, stream_mode="values"):
    print(step["messages"][-1])

# Stream LLM tokens (astream_events)
async for event in graph.astream_events(input, version="v2"):
    if event["event"] == "on_chat_model_stream":
        print(event["data"]["chunk"].content, end="")
```

## Persistence / Checkpointing

```python
from langgraph.checkpoint.memory import MemorySaver
# Production: from langgraph.checkpoint.sqlite import SqliteSaver

checkpointer = MemorySaver()
compiled = graph.compile(checkpointer=checkpointer)

# Thread-based conversation memory
config = {"configurable": {"thread_id": "session-42"}}
result1 = compiled.invoke({"messages": [HumanMessage("Hi")]}, config)
# Same thread picks up context:
result2 = compiled.invoke({"messages": [HumanMessage("What did I just say?")]}, config)
```

## Human-in-the-Loop

Interrupt the graph before or after specific nodes for human review:

```python
compiled = graph.compile(
    checkpointer=checkpointer,
    interrupt_before=["tool_execute"],  # Pause before tool runs
)

# Run until interrupt
result = compiled.invoke(input, config)
# Human reviews state, then resumes:
compiled.invoke(None, config)  # Continue from checkpoint
```

## FastAPI Serving

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
from langchain_core.messages import HumanMessage

app = FastAPI()

@app.post("/api/chat")
async def chat(request: ChatRequest):
    result = agent_graph.invoke({
        "messages": [HumanMessage(content=request.message)],
        "mode": request.mode or "general",
    })
    return {"response": result["messages"][-1].content}

@app.post("/api/chat/stream")
async def chat_stream(request: ChatRequest):
    async def generate():
        async for event in agent_graph.astream_events(
            {"messages": [HumanMessage(content=request.message)]},
            version="v2",
        ):
            if event["event"] == "on_chat_model_stream":
                token = event["data"]["chunk"].content
                if token:
                    yield f"data: {json.dumps({'content': token})}\n\n"
        yield "data: [DONE]\n\n"

    return StreamingResponse(generate(), media_type="text/event-stream")
```

## Observability with LangSmith

```python
import os
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_API_KEY"] = "ls-..."
os.environ["LANGCHAIN_PROJECT"] = "my-agent"

# All graph invocations are now traced automatically
# View at https://smith.langchain.com
```

## Error Handling

Handle tool errors gracefully with `handle_tool_errors`:

```python
tool_node = ToolNode(ALL_TOOLS, handle_tool_errors=True)
# Now tool errors become ToolMessage with error content
# instead of crashing the graph
```

## Structured Output for Routing

```python
from pydantic import BaseModel, Field
from typing import Literal

class Route(BaseModel):
    step: Literal["search", "analyze", "respond"] = Field(
        description="The next step to take"
    )
    reasoning: str = Field(description="Why this step was chosen")

router = llm.with_structured_output(Route)
decision = router.invoke(messages)
```

## Version Compatibility (2026)

| Package | Version | Notes |
|---------|---------|-------|
| langgraph | 1.x | Stable, production |
| langchain | 1.x | Core framework |
| langchain-ollama | 1.x | Local LLM via Ollama |
| langchain-anthropic | 0.3+ | Claude models |
| langchain-openai | 0.2+ | GPT models |

**Python 3.14 note**: Pydantic V1 warnings appear but are non-blocking. LangChain core works fine.
