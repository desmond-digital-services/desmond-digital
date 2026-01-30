# FF-Terminal Quick Start Guide

Get up and running with FF-Terminal in 5 minutes. This guide covers installation, basic configuration, and running your first autonomous agent.

## Prerequisites

- Node.js 18+ or Python 3.10+
- API access to a supported LLM (OpenAI, Anthropic, or local models)
- Basic familiarity with command-line tools

## Installation

```bash
# Clone the repository
git clone https://github.com/desmond-digital/ff-terminal.git
cd ff-terminal

# Install dependencies
npm install

# Or with Python
pip install ff-terminal
```

## Configure Your Environment

FF-Terminal uses environment variables for configuration. Create a `.env` file:

```bash
# Required: LLM API Key
export FF_LLM_API_KEY="your-api-key-here"

# Required: LLM Provider
export FF_LLM_PROVIDER="anthropic"  # or "openai", "local"

# Optional: Model selection
export FF_MODEL="claude-sonnet-4-20250514"

# Optional: Persistence directory
export FF_STORAGE_DIR="./.ff-storage"

# Optional: Log level
export FF_LOG_LEVEL="info"
```

## Create Your First Agent

Create a file named `my-agent.js`:

```javascript
const { Agent, Task, Planner } = require('ff-terminal');

const agent = new Agent({
  name: 'research-assistant',
  goal: 'Research and summarize information about autonomous systems',
  model: process.env.FF_MODEL || 'claude-sonnet-4-20250514',
  storageDir: process.env.FF_STORAGE_DIR || './.ff-storage'
});

// Define tasks
const tasks = [
  new Task({
    id: 'search',
    description: 'Search for recent developments in autonomous agent frameworks',
    execute: async (context) => {
      // Your search logic here
      return { results: [...] };
    }
  }),
  new Task({
    id: 'analyze',
    description: 'Analyze the search results and identify key trends',
    dependsOn: ['search'],
    execute: async (context) => {
      // Your analysis logic here
      return { trends: [...] };
    }
  }),
  new Task({
    id: 'summarize',
    description: 'Create a summary of findings',
    dependsOn: ['analyze'],
    execute: async (context) => {
      // Your summary logic here
      return { summary: '...' };
    }
  })
];

// Run the agent
async function main() {
  await agent.initialize();
  
  const planner = new Planner(agent, tasks);
  await planner.execute();
  
  const results = await agent.getResults();
  console.log('Agent completed:', results);
}

main().catch(console.error);
```

## Run Your Agent

```bash
# Execute the agent
node my-agent.js

# Run with verbose logging
DEBUG=ff:* node my-agent.js
```

## Verify Success

Check the output directory for results:

```bash
# View persisted state
cat .ff-storage/agent-state.json

# View task outputs
cat .ff-storage/tasks/summarize/output.json
```

## Next Steps

- **[Architecture Overview](ARCHITECTURE.md)** — Understand the headless design and agent lifecycle
- **[API Reference](API_REFERENCE.md)** — Explore full API documentation
- **[Examples](https://github.com/desmond-digital/ff-terminal/tree/main/examples)** — More agent templates

## Troubleshooting

| Issue | Solution |
|-------|----------|
| API key errors | Verify `FF_LLM_API_KEY` is set correctly |
| Connection timeouts | Check network connectivity; FF-Terminal supports offline operation once initialized |
| Memory issues | Reduce task complexity or increase Node.js memory: `node --max-old-space-size=4096` |

---

*FF-Terminal: autonomous by architecture, not by accident.*
