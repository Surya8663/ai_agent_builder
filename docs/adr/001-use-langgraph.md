# ADR 001: Adoption of LangGraph for Agent Orchestration

## Status
Accepted

## Date
2026-02-14

## Context
The "AI Agents Builder System" challenge suggested using **CrewAI** as a potential framework for orchestrating the multi-agent system. However, the requirements for this document intelligence platform include:
1.  **Cyclic Workflows**: The ability to loop back (e.g., Conflict Resolution -> Vision -> Fusion) which is essential for self-correction.
2.  **Fine-Grained State Management**: Passing a complex `DocumentState` object (containing images, text, bounding boxes, and confidence scores) between agents with precise control.
3.  **Deterministic Control Flow**: While we want "agentic" properties, the high-level process (Vision -> OCR -> Layout -> ...) is largely a directed graph with conditional branching, rather than a purely open-ended conversation.
4.  **Production Reliability**: Need for a framework that supports persistence (checkpointing), streaming, and explicit error handling paths.

## Decision
We decided to use **LangGraph** (built on top of LangChain) instead of CrewAI.

## Consequences

### Positive
*   **Superior Control**: LangGraph allows us to define the exact graph topology, including cycles and conditional edges, which maps perfectly to our pipeline architecture.
*   **State Persistence**: Checkpointing capabilities allow us to pause and resume workflows, or debug the state at any point in the graph.
*   **Explicit Error Handling**: We can define specific "error handler" nodes and edges, ensuring the pipeline doesn't just crash but handles failures gracefully.
*   **Async Support**: Native async support in LangGraph integrates well with our FastAPI backend for high-throughput processing.

### Negative
*   **Higher Boilerplate**: LangGraph requires more explicit definition of nodes and edges compared to the higher-level abstractions of CrewAI.
*   **Learning Curve**: It requires a deeper understanding of graph theory concepts and LangChain internals.

## Mitigation
We have managed the boilerplate by modularizing each agent into its own file and using valid Pydantic models (`DocumentState`) to enforce type safety across the graph, turning the "boilerplate" into "type-safe infrastructure".
