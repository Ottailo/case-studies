# Unified AI: a multi-agent system with staged external actions

Unified AI is a personal operating system I built to run long-lived workflows across coding, job discovery, market monitoring, writing, and personal operations. It is not a commercial product. It is a working system I use, and it is where I test ideas about agent orchestration, memory, and approval boundaries.

## The constraint

Most agent demos fail in the same place: the agent can call tools, but it cannot stay coherent across a long task. Context gets lost, tool results get mixed up, and the system either stops or keeps going after it should have asked for help.

The second constraint is trust. An agent that can send email, submit an application, or place an order needs a boundary between "prepared" and "done." A workflow that cannot stop for approval is not autonomous. It is unsupervised.

## The decision

I built the system around a small number of explicit invariants:

- State graphs for multi-step workflows, so a task has a current state instead of a growing chat log.
- Persistent memory in SQLite and a vector store, so facts survive a restart and can be retrieved by meaning.
- A tool layer with clear inputs and outputs, rather than free-form shell access from the model.
- Staged paths for external actions: the job-search workflow can prepare applications and emails, and dispatch is constrained by fit score, daily quota, dedupe, and an explicit environment gate. The live code still has auto-submit paths, so I do not claim a universal human approval gate.
- A local router that can send a request to different models instead of hard-coding one provider.

The alternative was a single long prompt with a pile of tools. It looks impressive in a demo and becomes impossible to debug when a tool fails halfway through.

## The implementation

The system runs as a FastAPI service with a Telegram control surface and a set of background workers. It uses LangGraph-style state transitions, CrewAI/AutoGen for delegated work, SQLite for durable local state, and a local gateway for model routing. The tool surface includes job discovery, CV tailoring, market telemetry, content drafting, and file and repository operations.

The job-search workflow is a useful example of the boundary. The system can scan roles, score fit, tailor a CV, and prepare an application. Dispatch channels exist and are constrained by fit score, daily quota, dedupe, and a staged path for review. The live code still has auto-submit paths, so I do not claim that every action waits for a human. The value is in the preparation and the audit trail; the next step is a stricter per-action approval gate.

## The result

The system runs daily on a small Oracle Cloud ARM instance. It has replaced a collection of one-off scripts with a single service that can be inspected, restarted, and extended. The most useful result is not a benchmark. It is that the workflows are boring enough to trust: the state is on disk, the tool calls are visible, and external actions have a staged path and an audit trail. The next step is a stricter per-action approval gate.

## What I would change

I would separate the personal assistant from the career and trading workflows much earlier. One service that can do everything is convenient to build and harder to reason about. The next version should have narrower services with explicit contracts, even if they share the same memory and model router.

## What stays private

The running instance contains personal data, credentials, job-search history, and private notes. The public case study describes the architecture and the decisions, not the live system.