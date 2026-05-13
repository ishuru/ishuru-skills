# langgraph-agent — Guide

## Quick Start

```
Build a LangGraph agent that [task]
Create a stateful agent with tools for [purpose]
```

## Core Concepts

| Concept | Description |
|---------|-------------|
| **StateGraph** | Graph with typed state flowing between nodes |
| **Nodes** | Python functions that read/write state |
| **Edges** | Conditional or fixed transitions between nodes |
| **Tools** | Functions the LLM can call during execution |
| **Checkpointing** | Persist state for resume/replay |
| **Streaming** | Token-by-token output as agent runs |

## Patterns

### ReAct Agent (Tool Loop)
```
User → Agent → (tool call?) → Tool → Agent → Response
```

### Multi-Agent Orchestration
```
Supervisor → { Researcher, Coder, Reviewer }
```

### Human-in-the-Loop
```
Agent → Interrupt → Human Review → Continue
```

## Dependencies

```bash
pip install langgraph langchain langchain-core
```
