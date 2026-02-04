# Desmond Digital

**Autonomous Agent Framework**

Desmond Digital provides tools for building and operating autonomous agents with flexible architecture and long-running persistence.

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

Desmond Digital offers a modular approach to autonomous agent development. The framework separates agent logic from LLM providers, enabling flexible deployment and long-running operations.

### Design Philosophy

The framework is built around two core principles:

- **Abstraction** — Agents interact with a provider interface rather than specific APIs
- **Persistence** — State is maintained across sessions, enabling multi-day workflows

### Architecture

```
Your Code -> Custom Agent Core -> Provider Abstraction -> Your Choice of LLM
                                          |
                         +----------------+----------------+
                         |                |                |
                    OpenAI           Anthropic         Local/Custom
```

This separation provides:
- Provider flexibility without code changes
- Transparent decision logging
- Extended session capabilities

---

## Key Features

| Feature | Description |
|---------|-------------|
| **Headless Operation** | Run agents without browser or UI dependencies. Suitable for servers and containers. |
| **Multi-Day Persistence** | State is preserved across sessions, enabling extended workflows. |
| **Provider Abstraction** | Single interface for OpenAI, Anthropic, local models, or custom backends. |
| **Modular Design** | Components can be extended or replaced independently. |
| **Minimal Footprint** | Lightweight dependencies with straightforward installation. |
| **Extensibility** | Plugin support for storage, monitoring, and notifications. |
| **Local Control** | Options for self-hosted deployment with no external dependencies. |
| **Observability** | Built-in logging for debugging and audit trails. |

---

## Quick Start

### Prerequisites

- Python 3.10 or higher
- pip or poetry
- Access to at least one LLM provider (optional for local testing)

### Installation

#### From Source

```bash
git clone https://github.com/desmond-digital-services/desmond-digital.git
cd desmond-digital
pip install -r requirements.txt
pip install -e .
```

#### Using pip

```bash
pip install desmond-digital
```

### Configuration

Create a `.env` file:

```bash
LLM_PROVIDER=openai
LLM_API_KEY=your-api-key-here
LLM_MODEL=gpt-4

AGENT_NAME=MyAgent
AGENT_PERSISTENCE_DIR=./agent_state
AGENT_LOG_LEVEL=INFO
```

### First Agent

```python
from desmond import Agent, Task

agent = Agent(
    name="MyFirstAgent",
    persistence_dir="./agent_state",
    llm_config={"provider": "openai", "model": "gpt-4"}
)

task = Task(
    description="Research quantum computing developments",
    goal="Summarize key breakthroughs from the last 6 months"
)

result = agent.run(task)
print(result.output)
```

---

## Architecture

Desmond Digital uses a layered architecture for flexibility:

```
+----------------------------------------------------------------------+
|                           Agent Layer                                  |
|  +-------------+  +-------------+  +-------------+                  |
|  |   Memory    |  |   Planning  |  |  Execution  |                  |
|  +-------------+  +-------------+  +-------------+                  |
+----------------------------------------------------------------------+
                                    |
                                    v
+----------------------------------------------------------------------+
|                          Core Engine                                  |
|  +-------------+  +-------------+  +-------------+                  |
|  |   Session   |  |   Context   |  |  Decision   |                  |
|  |   Manager   |  |   Manager   |  |   Engine    |                  |
|  +-------------+  +-------------+  +-------------+                  |
+----------------------------------------------------------------------+
                                    |
                                    v
+----------------------------------------------------------------------+
|                    Provider Abstraction Layer                         |
|  +-------------+  +-------------+  +-------------+                  |
|  |   OpenAI    |  |  Anthropic  |  |    Local    |                  |
|  |  Adapter    |  |   Adapter   |  |   Adapter   |                  |
|  +-------------+  +-------------+  +-------------+                  |
+----------------------------------------------------------------------+
                                    |
                                    v
+----------------------------------------------------------------------+
|                           Storage Layer                               |
|  +-------------+  +-------------+  +-------------+                  |
|  |   Memory    |  |   Vector    |  |   State     |                  |
|  |   Store     |  |   Store     |  |   Store     |                  |
|  +-------------+  +-------------+  +-------------+                  |
+----------------------------------------------------------------------+
```

### Component Roles

| Component | Responsibility |
|-----------|----------------|
| **Agent Layer** | Memory, planning, and execution behaviors |
| **Core Engine** | Session management, context handling, decisions |
| **Provider Abstraction** | Unified interface to LLM providers |
| **Storage Layer** | Persistent storage for memories and state |

---

## Comparison

| Feature | FF-Terminal | Claude Code | Codex CLI | Desmond Digital |
|---------|-------------|-------------|-----------|-----------------|
| **Headless** | No | No | Yes | Yes |
| **Persistence** | Session | Session | Session | Extended sessions |
| **Provider Abstraction** | No | No | No | Yes |
| **Self-Hosted** | No | No | No | Yes |
| **Open Source** | No | No | Yes | Yes |
| **Plugin System** | No | No | Yes | Yes |

### When to Use Each

| Framework | Best For |
|-----------|----------|
| **FF-Terminal** | Interactive CLI sessions, quick tasks |
| **Claude Code** | IDE-integrated development |
| **Codex CLI** | Terminal-based coding workflows |
| **Desmond Digital** | Extended automation, custom deployments |

---

## Use Cases

### Continuous Operations

Agents can run continuously with state preserved between sessions:

```python
agent = Agent(
    name="MonitorAgent",
    persistence_dir="./monitor_state",
    behavior="continuous"
)

while True:
    alerts = agent.check_systems()
    if alerts:
        agent.notify_team(alerts)
    agent.sleep(300)
```

### Multi-Stage Workflows

Complex processes that span hours or days:

1. Research — Gather information
2. Analysis — Process and synthesize
3. Creation — Generate outputs
4. Review — Quality verification
5. Delivery — Final distribution

### Server Deployment

Suitable for headless environments:

- CI/CD pipeline automation
- Data processing pipelines
- System administration tasks
- Background research and monitoring

### Industry Applications

| Domain | Applications |
|--------|--------------|
| **DevOps** | Incident response, log analysis, deployment monitoring |
| **Research** | Literature review, data collection, hypothesis testing |
| **Content** | Automated creation, curation, distribution |
| **Support** | Ticket triage, inquiry handling |
| **Finance** | Market monitoring, report generation, compliance |

---

## Contributing

Contributions are welcome. See [Contributing Guide](./docs/CONTRIBUTING.md).

### Getting Started

1. Fork the repository
2. Clone your fork
   ```bash
   git clone https://github.com/YOUR-USERNAME/desmond-digital.git
   cd desmond-digital
   ```
3. Create a feature branch
   ```bash
   git checkout -b feature/description
   ```
4. Install development dependencies
   ```bash
   pip install -r requirements-dev.txt
   ```
5. Make changes and add tests
6. Run tests
   ```bash
   pytest tests/
   ```
7. Submit a pull request

### Areas for Contribution

- Bug fixes and improvements
- New features and capabilities
- Documentation enhancements
- Testing and reliability
- Plugin development

---

## Documentation

| Topic | Link |
|-------|------|
| Quick Start | [./docs/QUICKSTART.md](./docs/QUICKSTART.md) |
| Architecture | [./docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) |
| API Reference | [./docs/API_REFERENCE.md](./docs/API_REFERENCE.md) |
| Configuration | [./docs/CONFIG.md](./docs/CONFIG.md) |
| Contributing | [./docs/CONTRIBUTING.md](./docs/CONTRIBUTING.md) |
| Plugins | [./docs/PLUGINS.md](./docs/PLUGINS.md) |
| Changelog | [./CHANGELOG.md](./CHANGELOG.md) |

---

## License

Licensed under MIT. See [LICENSE](./LICENSE).

---

## Contact

Maintained by [Desmond Digital Services](https://github.com/desmond-digital-services).

- Email: team@desmonddigital.dev
- Discord: [Join our server](https://discord.gg/desmonddigital)
- Twitter: [@DesmondDigital](https://twitter.com/DesmondDigital)

---

*Tools for autonomous agent development*
