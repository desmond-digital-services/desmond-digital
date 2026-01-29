# FF-Terminal vs. The Competition

## Technical Differentiation for Technical Buyers

| Feature | FF-Terminal | Claude Code | Codex CLI |
|---------|-------------|-------------|-----------|
| **Operation Mode** | Headless by default | Requires chat interface | Requires chat interface |
| **Context Persistence** | Multi-day, across sessions | Session-scoped | Session-scoped |
| **Architecture** | Custom framework | OpenAI wrapper | OpenAI wrapper |
| **Human Intervention** | Optional, not required | Required for decisions | Required for decisions |
| **Autonomy Model** | Agent-first, human oversight | Human-first, agent assist | Human-first, agent assist |
| **Offline Capability** | Designed for intermittent connectivity | Cloud-dependent | Cloud-dependent |
| **Multi-Step Execution** | Built-in persistence and retry | Prompt-dependent | Prompt-dependent |

---

## What This Means in Practice

**Claude Code and Codex CLI** are excellent tools. They wrap powerful models in usable interfaces. But they keep you in the conversation. Every significant decision flows through you. They're designed for developers who want AI assistance *while they work*—pair programming, code review, debugging.

**FF-Terminal** is for when you can't be in the conversation. When systems need to run unattended. When the cost of human intervention is higher than the cost of autonomous operation.

Same models under the hood. Different architecture entirely.

---

## Use Cases Where FF-Terminal Wins

- **24/7 operations** — Systems that run overnight, over weekends, during gaps in human availability
- **Multi-step workflows** — Tasks spanning hours or days that need persistent context
- **Headless automation** — No human in the loop, no chat interface required
- **Resource-constrained environments** — Designed for intermittent connectivity, not always-online architectures
- **Custom agent architectures** — Framework flexibility for building domain-specific agents

---

## Use Cases Where Claude Code/Codex CLI Win

- **Interactive development** — Pair programming, real-time code review
- **Exploratory work** — Understanding new codebases, rapid prototyping
- **Human-in-the-loop workflows** — When you're actively working and want AI assistance

---

## Bottom Line

| You're building... | Use this... |
|-------------------|-------------|
| Chat-based AI assistant for developers | Claude Code / Codex CLI |
| Autonomous agents that run unattended | FF-Terminal |

---

## The Architecture Difference

```
CLAUDE CODE / CODEX CLI
┌─────────────────────────────────────────┐
│         Chat Interface (You)             │
│                 ↓                       │
│         Prompt → Model → Response       │
│                 ↓                       │
│         You interpret, you act           │
└─────────────────────────────────────────┘
      You are the bottleneck

FF-TERMINAL
┌─────────────────────────────────────────┐
│         Agent Framework                  │
│                 ↓                       │
│    Task → Plan → Execute → Persist      │
│                 ↓                       │
│    Autonomous execution, report back     │
└─────────────────────────────────────────┘
      Agents are the workers
```

---

## Integration Options

FF-Terminal is a framework, not a hosted service. Integration paths include:

- **Direct framework integration** — Embed FF-Terminal in your codebase
- **API wrapper** — Expose FF-Terminal capabilities via your own API
- **Custom agent development** — We build domain-specific agents on the framework

---

*FF-Terminal: autonomous by architecture, not by accident.*
