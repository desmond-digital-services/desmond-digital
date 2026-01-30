# FF-Terminal API Reference

Complete API documentation for FF-Terminal's core interfaces and classes.

## Table of Contents

- [Agent](#agent)
- [Task](#task)
- [Planner](#planner)
- [Context](#context)
- [Storage](#storage)
- [LLM Adapter](#llm-adapter)
- [Configuration](#configuration)

---

## Agent

The core class representing an autonomous agent.

### Constructor

```typescript
new Agent(config: AgentConfig): Agent
```

### AgentConfig

```typescript
interface AgentConfig {
  // Required
  name: string;              // Unique agent identifier
  goal: string;              // Agent's primary objective
  
  // LLM Configuration
  model?: string;            // Model identifier (default: provider default)
  provider?: string;         // LLM provider (default: from env)
  
  // Storage
  storageDir?: string;       // Persistence directory
  
  // Autonomy
  autonomy?: AutonomyConfig; // Execution boundaries
  
  // Callbacks
  onTaskComplete?: (task: Task, result: Result) => void;
  onTaskFail?: (task: Task, error: Error) => void;
  onAgentStateChange?: (state: AgentState) => void;
}
```

### Agent Methods

#### initialize()

```typescript
async initialize(): Promise<void>
```

Initializes the agent, establishes LLM connection, and loads persisted state.

#### start()

```typescript
async start(): Promise<void>
```

Begins agent execution. The agent will run until completion or interruption.

#### pause()

```typescript
async pause(): Promise<void>
```

Pauses execution, persists current state. Agent can be resumed later.

#### resume()

```typescript
async resume(): Promise<void>
```

Resumes a paused agent from its persisted state.

#### stop()

```typescript
async stop(): Promise<void>
```

Stops the agent and cleans up resources. Final state is persisted.

#### getState()

```typescript
getState(): AgentState
```

Returns the current agent state.

```typescript
type AgentState = 
  | 'created'
  | 'initialized'
  | 'running'
  | 'paused'
  | 'completed'
  | 'failed';
```

#### getResults()

```typescript
async getResults(): Promise<AgentResults>
```

Returns all task results.

```typescript
interface AgentResults {
  agentId: string;
  completedAt: Date;
  duration: number;
  tasks: TaskResult[];
  summary: string;
}
```

---

## Task

Represents an atomic unit of work.

### Constructor

```typescript
new Task(config: TaskConfig): Task
```

### TaskConfig

```typescript
interface TaskConfig {
  // Required
  id: string;              // Unique identifier within agent
  description: string;     // Human-readable description
  
  // Dependencies
  dependsOn?: string[];    // Task IDs that must complete first
  
  // Execution
  execute: ExecuteFn;      // Task implementation
  timeout?: number;        // Max execution time in ms
  
  // Retry
  retryPolicy?: RetryPolicy;
  
  // Metadata
  tags?: string[];
  priority?: number;       // 1-10, higher = more important
}
```

### ExecuteFn

```typescript
type ExecuteFn = (
  context: Context,
  inputs: Record<string, unknown>
) => Promise<unknown>;
```

### RetryPolicy

```typescript
interface RetryPolicy {
  maxAttempts: number;       // Default: 3
  initialDelayMs: number;    // Default: 1000
  maxDelayMs: number;        // Default: 60000
  backoffMultiplier: number; // Default: 2
  retryOn: string[];         // Error types to retry
}
```

---

## Planner

Orchestrates task execution based on dependencies and priorities.

### Constructor

```typescript
new Planner(agent: Agent, tasks: Task[]): Planner
```

### Planner Methods

#### execute()

```typescript
async execute(): Promise<ExecutionResult>
```

Runs all tasks respecting dependencies. Blocks until completion or failure.

#### executeAsync()

```typescript
async executeAsync(): Promise<void>
```

Starts execution without blocking. Use event listeners to track progress.

#### getExecutionGraph()

```typescript
getExecutionGraph(): ExecutionGraph
```

Returns the dependency graph visualization.

```typescript
interface ExecutionGraph {
  nodes: Task[];
  edges: DependencyEdge[];
  criticalPath: string[];
}
```

---

## Context

Provides agents with accumulated state and utilities.

### Context Properties

```typescript
interface Context {
  // Identity
  agent: {
    id: string;
    name: string;
    goal: string;
  };
  
  // State
  state: {
    current: Record<string, unknown>;
    memory: Record<string, unknown>;
  };
  
  // Task results
  results: {
    get(taskId: string): unknown;
    getAll(): Record<string, unknown>;
  };
  
  // Utilities
  logger: Logger;
  storage: StorageAdapter;
  llm: LLMAdapter;
}
```

### Context Methods

#### log()

```typescript
context.log(level: LogLevel, message: string, data?: unknown): void
```

```typescript
type LogLevel = 'debug' | 'info' | 'warn' | 'error';
```

#### remember()

```typescript
context.remember(key: string, value: unknown): void
```

Stores information in agent memory (persists across sessions.

#### recall()

```typescript
context.recall(key: string): unknown
```

Retrieves information from agent memory.

---

## Storage

Manages persistent data.

### Storage Methods

#### saveState()

```typescript
async saveState(key: string, data: unknown): Promise<void>
```

Persists data to storage.

#### loadState()

```typescript
async loadState(key: string): Promise<unknown | null>
```

Retrieves persisted data.

#### listStates()

```typescript
async listStates(prefix?: string): Promise<string[]>
```

Lists all stored keys.

#### deleteState()

```typescript
async deleteState(key: string): Promise<void>
```

Removes stored data.

#### clear()

```typescript
async clear(): Promise<void>
```

Clears all stored data.

---

## LLM Adapter

Abstracts LLM provider interactions.

### Supported Providers

```typescript
type LLMProvider = 'openai' | 'anthropic' | 'local';
```

### Adapter Methods

#### complete()

```typescript
async complete(prompt: string, context: Context): Promise<string>
```

Sends a prompt and returns the full response.

#### stream()

```typescript
async *stream(prompt: string, context: Context): AsyncGenerator<string>
```

Yields response chunks as they become available.

#### countTokens()

```typescript
async countTokens(text: string): Promise<number>
```

Returns the token count for text.

#### getMaxTokens()

```typescript
async getMaxTokens(): Promise<number>
```

Returns the model's maximum context window.

---

## Configuration

### Environment Variables

| Variable | Required | Description | Default |
|----------|----------|-------------|---------|
| `FF_LLM_API_KEY` | Yes | LLM provider API key | - |
| `FF_LLM_PROVIDER` | No | LLM provider | `anthropic` |
| `FF_MODEL` | No | Model identifier | Provider default |
| `FF_STORAGE_DIR` | No | Persistence directory | `./.ff-storage` |
| `FF_LOG_LEVEL` | No | Minimum log level | `info` |
| `FF_MAX_RETRIES` | No | Task retry attempts | `3` |
| `FF_TASK_TIMEOUT` | No | Default task timeout (ms) | `300000` |

### Configuration File

FF-Terminal can read configuration from `ff.config.json`:

```json
{
  "agent": {
    "name": "my-agent",
    "goal": "Automate complex workflows"
  },
  "llm": {
    "provider": "anthropic",
    "model": "claude-sonnet-4-20250514"
  },
  "storage": {
    "directory": "./.ff-storage"
  },
  "autonomy": {
    "maxRetries": 3,
    "taskTimeout": 300000,
    "requireApprovalFor": ["delete", "network"]
  }
}
```

---

## Events

FF-Terminal emits events for monitoring:

```typescript
agent.on('task:complete', (task: Task, result: Result) => {
  console.log(`Task ${task.id} completed`);
});

agent.on('task:fail', (task: Task, error: Error) => {
  console.error(`Task ${task.id} failed:`, error);
});

agent.on('state:change', (state: AgentState) => {
  console.log(`Agent state: ${state}`);
});

agent.on('error', (error: Error) => {
  console.error('Agent error:', error);
});
```

### Event Types

| Event | Payload | Description |
|-------|---------|-------------|
| `task:enqueued` | Task | Task added to queue |
| `task:started` | Task | Task execution began |
| `task:complete` | Task, Result | Task completed successfully |
| `task:fail` | Task, Error | Task failed |
| `task:retry` | Task, Error, Attempt | Task retry initiated |
| `state:change` | AgentState | Agent state transitioned |
| `agent:complete` | AgentResults | Agent finished all tasks |
| `error` | Error | Unhandled error |

---

## Type Summary

### Core Types

```typescript
// Agent
type AgentState = 'created' | 'initialized' | 'running' | 'paused' | 'completed' | 'failed';

// Task
type TaskStatus = 'pending' | 'running' | 'completed' | 'failed' | 'skipped';

// Logging
type LogLevel = 'debug' | 'info' | 'warn' | 'error';

// Errors
type ErrorSeverity = 'retryable' | 'recoverable' | 'fatal' | 'escalate';
```

---

## Examples

### Basic Agent

```typescript
import { Agent, Task } from 'ff-terminal';

const agent = new Agent({
  name: 'example-agent',
  goal: 'Demonstrate basic functionality'
});

const tasks = [
  new Task({
    id: 'hello',
    description: 'Print a greeting',
    execute: async (ctx) => {
      ctx.log('info', 'Hello from FF-Terminal!');
      return { greeting: 'Hello, World!' };
    }
  })
];

await agent.initialize();
await agent.start();
console.log(agent.getResults());
```

### With Dependencies

```typescript
const tasks = [
  new Task({
    id: 'fetch',
    description: 'Fetch data from API',
    execute: async (ctx) => {
      return { data: await fetchData() };
    }
  }),
  new Task({
    id: 'process',
    description: 'Process the data',
    dependsOn: ['fetch'],
    execute: async (ctx) => {
      const data = ctx.results.get('fetch');
      return { processed: transform(data) };
    }
  }),
  new Task({
    id: 'save',
    description: 'Save results',
    dependsOn: ['process'],
    execute: async (ctx) => {
      const processed = ctx.results.get('process');
      await saveResults(processed);
    }
  })
];
```

---

*FF-Terminal: autonomous by architecture, not by accident.*
