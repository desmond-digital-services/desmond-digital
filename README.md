# Desmond Digital

**Anti-Wrapper Autonomous Agent Framework**

Desmond Digital is a revolutionary agent framework designed to operate autonomously without wrapping existing AI services. Unlike traditional approaches that wrap APIs, Desmond Digital implements a custom architecture that gives you complete control over your autonomous agents.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/Version-1.0.0-blue.svg)](./CHANGELOG.md)

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Quick Start](#quick-start)
- [Architecture](#architecture)
- [Comparison](#comparison)
- [Use Cases](#use-cases)
- [Contributing](#contributing)
- [Documentation](#documentation)
- [License](#license)

---

## Overview

Desmond Digital represents a paradigm shift in autonomous agent development. While most frameworks simply wrap OpenAI's API, Anthropic's API, or other services, Desmond Digital builds a **custom architecture** that decouples your agents from any single AI provider.

### What is an "Anti-Wrapper" Framework?

Traditional agent frameworks look like this:
```
Your Code -> Wrapper Library -> OpenAI/Anthropic API -> Black Box
```

Desmond Digital's architecture:
```
Your Code -> Custom Agent Core -> Multi-Provider Abstraction -> Your Choice of LLM
                                             |
                        +---------------------+---------------------+
                        |                     |                     |
                   OpenAI              Anthropic            Local/Custom
```

This means:
- **No vendor lock-in** — Switch providers without rewriting your agents
- **Full transparency** — Inspect every decision your agent makes
- **Custom behavior** — Implement domain-specific logic beyond prompt engineering
- **Multi-day persistence** — Agents can maintain context across sessions indefinitely

---

## Key Features

| Feature | Description |
|---------|-------------|
| **Headless Operation** | Run agents completely headless with no browser or UI dependencies. Perfect for servers, containers, and CI/CD pipelines. |
| **Multi-Day Persistence** | Agents maintain memory, context, and state across sessions, days, or weeks. Never lose progress or context again. |
| **Custom Architecture** | Built from scratch with modular components. No API wrappers, no black boxes. Full control over agent behavior. |
| **Provider Agnostic** | Use any LLM provider (OpenAI, Anthropic, local models, custom backends) without changing your agent code. |
| **Lightweight** | Minimal dependencies, fast startup, low resource footprint. Runs anywhere Python runs. |
| **Plugin System** | Extend functionality with plugins for storage, monitoring, notifications, and more. |
| **Security First** | Built with security in mind. No data leaves your infrastructure unless you configure it to. |
| **Observability** | Built-in logging, metrics, and tracing to understand exactly what your agents are doing. |

---

## Quick Start

### Prerequisites

- Python 3.10 or higher
- pip or poetry
- Access to at least one LLM provider (optional for local testing)

### Installation

#### From Source

```bash
# Clone the repository
git clone https://github.com/desmond-digital-services/desmond-digital.git
cd desmond-digital

# Install dependencies
pip install -r requirements.txt

# Install Desmond Digital
pip install -e .
```

#### Using pip

```bash
pip install desmond-digital
```

### Configuration

Create a `.env` file in your project directory:

```bash
# LLM Provider Configuration
LLM_PROVIDER=openai  # Options: openai, anthropic, local, custom
LLM_API_KEY=your-api-key-here
LLM_MODEL=gpt-4

# Optional: Alternative providers
ANTHROPIC_API_KEY=your-anthropic-key
LOCAL_MODEL_URL=http://localhost:8000/v1

# Agent Settings
AGENT_NAME=MyAgent
AGENT_PERSISTENCE_DIR=./agent_state
AGENT_LOG_LEVEL=INFO
```

### Your First Agent

Create `my_agent.py`:

```python
from desmond import Agent, Task

# Initialize your agent
agent = Agent(
    name="MyFirstAgent",
    persistence_dir="./agent_state",
    llm_config={
        "provider": "openai",
        "model": "gpt-4"
    }
)

# Define a task
task = Task(
    description="Research the latest developments in quantum computing",
    goal="Summarize key breakthroughs from the last 6 months"
)

# Run the agent
result = agent.run(task)
print(result.output)
```

Run your agent:

```bash
python my_agent.py
```

---

## Architecture

Desmond Digital follows a modular, layered architecture designed for flexibility and extensibility.

```
+----------------------------------------------------------------------+
|                         Agent Layer                                   |
|  +-------------+  +-------------+  +-------------+                   |
|  |   Memory    |  |   Planning  |  |  Execution  |                   |
|  |   Module    |  |   Module    |  |   Module    |                   |
|  +-------------+  +-------------+  +-------------+                   |
+----------------------------------------------------------------------+
                                  |
                                  v
+----------------------------------------------------------------------+
|                       Core Engine Layer                               |
|  +-------------+  +-------------+  +-------------+                   |
|  |   Session   |  |   Context   |  |   Decision  |                   |
|  |   Manager   |  |   Manager   |  |   Engine    |                   |
|  +-------------+  +-------------+  +-------------+                   |
+----------------------------------------------------------------------+
                                  |
                                  v
+----------------------------------------------------------------------+
|                     Provider Abstraction Layer                        |
|  +-------------+  +-------------+  +-------------+                   |
|  |   OpenAI    |  |  Anthropic  |  |    Local    |                   |
|  |  Adapter    |  |   Adapter   |  |   Adapter   |                   |
|  +-------------+  +-------------+  +-------------+                   |
+----------------------------------------------------------------------+
                                  |
                                  v
+----------------------------------------------------------------------+
|                        Storage Layer                                  |
|  +-------------+  +-------------+  +-------------+                   |
|  |   Memory    |  |   Vector    |  |   State     |                   |
|  |   Store     |  |   Store     |  |   Store     |                   |
|  +-------------+  +-------------+  +-------------+                   |
+----------------------------------------------------------------------+
```

### Component Descriptions

| Component | Responsibility |
|-----------|----------------|
| **Agent Layer** | High-level agent behaviors including memory, planning, and execution |
| **Core Engine** | Session management, context handling, and decision-making logic |
| **Provider Abstraction** | Unified interface to different LLM providers |
| **Storage Layer** | Persistent storage for memories, vectors, and agent state |

---

## Comparison

| Feature | FF-Terminal | Claude Code | Codex CLI | Desmond Digital |
|---------|-------------|-------------|-----------|-----------------|
| **Headless Operation** | No | No | Yes | Yes |
| **Multi-Day Persistence** | No | No | No | Yes |
| **Custom Architecture** | No | No | No | Yes |
| **Provider Agnostic** | No | No | No | Yes |
| **No API Wrapping** | No | No | No | Yes |
| **Open Source** | No | No | Yes | Yes |
| **Self-Hosted** | No | No | No | Yes |
| **Plugin System** | No | No | Yes | Yes |
| **Memory Persistence** | Session | Session | Session | Indefinite |
| **Transparency** | Limited | Limited | Limited | Full |

### Detailed Breakdown

| Framework | Vendor Lock-in | Use Case | Best For |
|-----------|----------------|----------|----------|
| **FF-Terminal** | High (Claude) | Interactive CLI | Quick coding tasks |
| **Claude Code** | High (Claude) | IDE integration | Developers in VS Code |
| **Codex CLI** | Moderate (OpenAI) | Command-line coding | Terminal workflows |
| **Desmond Digital** | None | Production automation | 24/7 ops, custom workflows |

---

## Use Cases

### 24/7 Operations

Run agents that never sleep. Desmond Digital's persistence layer means your agents can:

- Monitor systems around the clock
- Handle incidents while you're away
- Process queues continuously
- Maintain long-running research tasks

```python
# Example: Continuous monitoring agent
agent = Agent(
    name="MonitorAgent",
    persistence_dir="./monitor_state",
    behavior="continuous"
)

while True:
    alerts = agent.check_systems()
    if alerts:
        agent.notify_team(alerts)
    agent.sleep(300)  # 5-minute breaks
```

### Multi-Step Workflows

Automate complex workflows that span hours or days:

1. **Research Phase** — Gather information
2. **Analysis Phase** — Process and synthesize
3. **Creation Phase** — Generate outputs
4. **Review Phase** — Quality checks
5. **Delivery Phase** — Send results

```python
workflow = Workflow([
    Step("research", agent.research_topic),
    Step("analyze", agent.analyze_findings),
    Step("create", agent.generate_report),
    Step("review", agent.quality_check),
    Step("deliver", agent.send_to_stakeholders)
])

result = workflow.execute()
```

### Headless Automation

Perfect for server environments without displays:

- **CI/CD Pipelines** — Automated code review and testing
- **Data Pipelines** — Continuous data processing
- **Server Maintenance** — Automated system administration
- **Background Research** — Long-running information gathering

### Specific Applications

| Domain | Use Case |
|--------|----------|
| **DevOps** | Automated incident response, log analysis, deployment monitoring |
| **Research** | Literature review, data collection, hypothesis testing |
| **Content** | Automated content creation, curation, and distribution |
| **Support** | 24/7 customer inquiry handling, ticket triage |
| **Finance** | Market monitoring, report generation, compliance checking |
| **Healthcare** | Patient monitoring, appointment scheduling, research assistance |

---

## Contributing

We welcome contributions! Please see our [Contributing Guide](./docs/CONTRIBUTING.md) for details.

### Getting Started

1. **Fork the repository**
2. **Clone your fork**
   ```bash
   git clone https://github.com/YOUR-USERNAME/desmond-digital.git
   cd desmond-digital
   ```
3. **Create a feature branch**
   ```bash
   git checkout -b feature/amazing-feature
   ```
4. **Install development dependencies**
   ```bash
   pip install -r requirements-dev.txt
   ```
5. **Make your changes** and add tests
6. **Run tests**
   ```bash
   pytest tests/
   ```
7. **Submit a pull request**

### Contribution Areas

- **Bug fixes** — Help us squash bugs
- **Features** — Add new capabilities
- **Documentation** — Improve guides and examples
- **UX/UI** — Enhance CLI and API usability
- **Testing** — Increase test coverage
- **Plugins** — Create plugins for other tools

---

## Documentation

| Topic | Link |
|-------|------|
| **Full Documentation** | [./docs/README.md](./docs/README.md) |
| **API Reference** | [./docs/API.md](./docs/API.md) |
| **Configuration Guide** | [./docs/CONFIG.md](./docs/CONFIG.md) |
| **Architecture Deep Dive** | [./docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) |
| **Plugin Development** | [./docs/PLUGINS.md](./docs/PLUGINS.md) |
| **Examples & Tutorials** | [./examples/](./examples/) |
| **Changelog** | [./CHANGELOG.md](./CHANGELOG.md) |
| **Migration Guide** | [./docs/MIGRATION.md](./docs/MIGRATION.md) |

---

## License

Desmond Digital is licensed under the MIT License. See [LICENSE](./LICENSE) for details.

---

## About

Desmond Digital is maintained by [Desmond Digital Services](https://github.com/desmond-digital-services). We're building the future of autonomous agents.

**Questions? Reach out:**
- Email: team@desmonddigital.dev
- Discord: [Join our server](https://discord.gg/desmonddigital)
- Twitter: [@DesmondDigital](https://twitter.com/DesmondDigital)

---

*Making autonomous agents work for everyone*
