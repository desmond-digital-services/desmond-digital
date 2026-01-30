# FF-Terminal Architecture

深入了解 FF-Terminal 的系统架构，包括无头设计、持久化机制和代理生命周期。

## Overview

FF-Terminal is an **autonomous agent framework** built for headless operation. Unlike chat-based AI tools that keep humans in the loop, FF-Terminal is designed from the ground up for agents that run unattended, persist across sessions, and make decisions without constant human intervention.

```
┌─────────────────────────────────────────────────────────────────┐
│                      FF-Terminal Framework                       │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐   ┌─────────────┐   ┌─────────────┐           │
│  │   Agent     │   │   Planner   │   │  Executor   │           │
│  │   Core      │ ←→ │   Engine    │ ←→ │   Engine    │           │
│  └─────────────┘   └─────────────┘   └─────────────┘           │
│         ↓                  ↓                  ↓                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │              Persistent State Layer                      │   │
│  │    (Context • Memory • Task State • Configuration)      │   │
│  └─────────────────────────────────────────────────────────┘   │
│                          ↓                                      │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                 LLM Adapter Interface                    │   │
│  │              (OpenAI • Anthropic • Local)               │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## Core Design Principles

### 1. Headless by Default

Most "autonomous" agent tools are conversational interfaces in disguise. They wrap language models in chat UIs and call it autonomy. FF-Terminal takes a different approach:

| Aspect | FF-Terminal | Chat-Based Tools |
|--------|-------------|------------------|
| Operation | Headless execution | Requires human in loop |
| Decision making | Agent determines path | Human approves each step |
| Interface | API/Code | Chat UI |
| Supervision | Optional boundaries | Constant intervention |

Headless operation means:
- Agents run without attached terminals
- No human presence required during execution
- Results reported asynchronously
- Errors handled with retry logic, not human escalation

### 2. Multi-Day Persistence

FF-Terminal agents maintain context across sessions, days, and even weeks. This is critical for:

- Long-running workflows spanning hours or days
- Tasks that must resume after system restarts
- Memory accumulation over extended operation periods

```
Session 1                    Session 2                    Session 3
┌─────────────┐             ┌─────────────┐             ┌─────────────┐
│ Initialize  │             │   Resume    │             │   Resume    │
│ Load State  │────────────→│  Load State │────────────→│  Load State │
│ Execute     │             │  Continue   │             │  Continue   │
│ Persist     │────────────→│  Persist    │────────────→│  Persist    │
└─────────────┘             └─────────────┘             └─────────────┘
```

### 3. Agent Lifecycle

Agents in FF-Terminal follow a defined lifecycle:

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Agent Lifecycle                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   CREATED ──→ INITIALIZED ──→ RUNNING ──→ PAUSED ──→ COMPLETED     │
│       │              │              │           │         │          │
│       │              │              │           │         │          │
│       └──────────────┴──────────────┴───────────┴─────────┘          │
│                         (can transition back)                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

#### State Descriptions

| State | Description |
|-------|-------------|
| **CREATED** | Agent instance exists, configuration loaded |
| **INITIALIZED** | LLM connection established, persistent storage initialized |
| **RUNNING** | Actively executing tasks, making decisions |
| **PAUSED** | Execution suspended, state persisted, can resume |
| **COMPLETED** | All tasks finished, final results stored |

### 4. Task Execution Model

Tasks are the atomic units of work in FF-Terminal:

```typescript
interface Task {
  id: string;                    // Unique identifier
  description: string;           // Human-readable description
  dependsOn?: string[];          // Task dependencies
  execute: (context: Context) → Promise<Result>;
  retryPolicy?: RetryPolicy;     // Automatic retry on failure
  timeout?: number;              // Max execution time (ms)
}
```

**Execution Flow:**

```
Task Queue
    ↓
┌──────────────┐
│ Dependencies │──── Yes ──→ Wait for dependencies
│  Resolved?   │
└──────────────┘
    ↓ No
┌──────────────┐
│  Execute     │──── Success ──→ Persist result
│  Task        │                        ↓
└──────────────┘              ┌──────────────┐
    ↓ Failure                │  Mark Done   │
┌──────────────┐             │  Check       │
│  Retry       │             │  Dependents  │
│  or Fail?    │             └──────────────┘
└──────────────┘                   ↓
    │                              ↓
    ├─ Retry limit ──→ Fail task
    └─ Retry success ──→ Persist result
```

### 5. Persistence Layer

FF-Terminal persists state to disk for recovery and multi-session continuity:

```
.ff-storage/
├── agent-state.json        # Agent metadata, configuration
├── context.json            # LLM conversation context
├── memory.json             # Long-term agent memory
├── tasks/
│   ├── task-001/
│   │   ├── input.json
│   │   ├── output.json
│   │   └── state.json
│   └── task-002/
└── logs/
    ├── execution.log
    └── errors.log
```

#### State Types

| File | Purpose | Retention |
|------|---------|-----------|
| `agent-state.json` | Agent configuration, current status | Permanent |
| `context.json` | LLM context window contents | Session-based |
| `memory.json` | Learned patterns, accumulated knowledge | Permanent |
| `tasks/*/output.json` | Task results | Until cleared |
| `logs/*.log` | Execution logs | Rotating |

### 6. LLM Adapter Interface

FF-Terminal abstracts LLM providers through a unified adapter interface:

```typescript
interface LLMAdapter {
  // Initialize connection
  initialize(config: LLMConfig): Promise<void>;
  
  // Send prompt and get response
  complete(prompt: string, context: Context): Promise<string>;
  
  // Stream response (for long outputs)
  stream(prompt: string, context: Context): AsyncIterator<string>;
  
  // Check model availability
  health(): Promise<HealthStatus>;
}
```

**Supported Adapters:**
- OpenAI (GPT-4, GPT-3.5)
- Anthropic (Claude 3, Claude 2)
- Local models (Ollama, LM Studio, vLLM)

### 7. Autonomy Boundaries

Agents operate within configurable boundaries:

```typescript
interface AutonomyConfig {
  // Maximum retries per task
  maxRetries: number;
  
  // Timeout for individual tasks
  taskTimeout: number;
  
  // Maximum total execution time
  maxRuntime: number;
  
  // Require approval for high-risk operations
  requireApprovalFor: string[];
  
  // Resource limits
  maxMemoryMb: number;
  maxDiskGb: number;
  
  // Network restrictions
  allowedDomains: string[];
  blockedDomains: string[];
}
```

### 8. Error Handling & Recovery

FF-Terminal implements robust error handling:

```typescript
class ErrorHandler {
  // Classify error severity
  classifyError(error: Error): ErrorSeverity;
  
  // Determine recovery strategy
  determineRecovery(error: Error): RecoveryStrategy;
  
  // Execute recovery
  async recover(error: Error): Promise<void>;
}
```

**Error Categories:**

| Severity | Example | Recovery Strategy |
|----------|---------|-------------------|
| **RETRYABLE** | Network timeout, rate limit | Exponential backoff retry |
| **RECOVERABLE** | LLM hallucination, invalid output | Regenerate with corrected prompt |
| **FATAL** | Invalid configuration, disk full | Stop and report |
| **ESCALATE** | Unhandled exception | Human notification |

## Performance Characteristics

| Metric | Value | Notes |
|--------|-------|-------|
| Cold start | < 2s | LLM connection establishment |
| Task switching | < 100ms | State serialization |
| Recovery time | < 5s | From persisted state |
| Memory per agent | ~50MB | Base overhead |
| Storage per task | ~1-10KB | Depends on output size |

## Comparison with Alternatives

| Aspect | FF-Terminal | Claude Code | Codex CLI |
|--------|-------------|-------------|-----------|
| Architecture | Custom framework | OpenAI wrapper | OpenAI wrapper |
| Persistence | Multi-day | Session-scoped | Session-scoped |
| Autonomy | Agent-first | Human-first | Human-first |
| Interface | Headless API | Chat UI | Chat UI |
| Offline mode | Supported | Limited | Limited |

---

## Summary

FF-Terminal's architecture reflects its origin: a tool built for real-world autonomy, where humans can't be present for every decision. The framework provides:

1. **Headless execution** — Agents run without human intervention
2. **Persistence** — State survives restarts, sessions, and time
3. **Lifecycle management** — Clear state transitions and control
4. **Error resilience** — Automatic recovery and retry logic
5. **LLM flexibility** — Provider-agnostic adapter interface

This architecture enables agents that work in the real world—where networks fail, systems restart, and humans sleep.

---

*FF-Terminal: autonomous by architecture, not by accident.*
