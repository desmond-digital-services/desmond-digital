# Contributing to FF-Terminal

Thank you for your interest in contributing to FF-Terminal! This document outlines the process for contributing to the project.

## Ways to Contribute

- **Bug Reports** — Found an issue? Let us know
- **Feature Requests** — Have an idea? Share it
- **Code Contributions** — Fix bugs, add features, improve docs
- **Documentation** — Improve guides, examples, and tutorials
- **Testing** — Help us maintain quality

## Getting Started

### Prerequisites

- Node.js 18+ or Python 3.10+
- Git
- An LLM API key (OpenAI or Anthropic) for full testing

### Setting Up Your Environment

```bash
# Clone the repository
git clone https://github.com/desmond-digital/ff-terminal.git
cd ff-terminal

# Install dependencies
npm install

# Verify installation
npm test
```

### Understanding the Architecture

Before contributing, we recommend:

1. Read the [Architecture Overview](ARCHITECTURE.md)
2. Review the [API Reference](API_REFERENCE.md)
3. Explore the [examples/](https://github.com/desmond-digital/ff-terminal/tree/main/examples) directory

## Development Workflow

### 1. Create a Feature Branch

```bash
# Update main branch
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/your-feature-name
```

### 2. Make Changes

Follow these guidelines:

- **Code Style**: Use the project's ESLint/Prettier configuration
- **Types**: Add TypeScript types for all public APIs
- **Tests**: Add tests for new functionality
- **Documentation**: Update docs for new features
- **Commits**: Use conventional commit messages

### 3. Run Tests

```bash
# Run all tests
npm test

# Run specific test file
npm test -- --testPathPattern=agent.test.ts

# Run with coverage
npm test -- --coverage
```

### 4. Submit a Pull Request

```bash
# Commit your changes
git add .
git commit -m "feat: add new feature description"

# Push to GitHub
git push origin feature/your-feature-name
```

Then open a Pull Request on GitHub.

## Pull Request Guidelines

### Required Checklist

- [ ] Code follows project style guidelines
- [ ] Tests pass (add new tests for new functionality)
- [ ] Documentation updated
- [ ] No breaking changes (or clearly documented)
- [ ] Commit messages follow conventions

### Commit Message Format

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types:**
- `feat` — New feature
- `fix` — Bug fix
- `docs` — Documentation changes
- `style` — Code style changes (formatting, etc.)
- `refactor` — Code refactoring
- `test` — Adding tests
- `chore` — Maintenance tasks

**Example:**
```
feat(agent): add pause/resume functionality

Allows agents to be paused and resumed from persisted state.
Useful for long-running workflows that need to stop and continue.

Closes #123
```

### Code Review Process

1. All PRs require review from maintainers
2. Address feedback promptly
3. Request re-review after making changes
4. PRs must pass CI before merge

## Project Structure

```
ff-terminal/
├── src/
│   ├── core/           # Agent, Task, Planner core classes
│   ├── adapters/       # LLM provider adapters
│   ├── storage/        # Persistence layer
│   └── utils/          # Helper functions
├── docs/               # Documentation
├── examples/           # Example agents
├── tests/              # Test files
├── package.json
└── tsconfig.json
```

## Issue Reporting

### Bug Reports

Include:

- Clear description of the bug
- Steps to reproduce
- Expected behavior
- Actual behavior
- Environment (OS, Node version, etc.)
- Error messages and stack traces

### Feature Requests

Include:

- Clear description of the feature
- Use case (why is this useful?)
- Suggested implementation approach
- Alternative solutions considered

## Code Style Guidelines

### TypeScript

```typescript
// Use explicit types for public APIs
function processTask(input: TaskInput): Promise<TaskOutput> {
  // ...
}

// Use interfaces for configuration objects
interface AgentConfig {
  name: string;
  goal: string;
  model?: string;
}

// Prefer async/await over callbacks
async function execute(): Promise<void> {
  const result = await someAsyncOperation();
}
```

### Error Handling

```typescript
// Use custom error classes
class TaskExecutionError extends Error {
  constructor(
    message: string,
    public taskId: string,
    public cause?: Error
  ) {
    super(message);
    this.name = 'TaskExecutionError';
  }
}

// Throw errors with context
if (!requiredInput) {
  throw new TaskExecutionError(
    'Missing required input',
    this.id,
    new Error('input validation failed')
  );
}
```

### Testing

```typescript
// Use descriptive test names
describe('Agent', () => {
  describe('initialization', () => {
    it('should load persisted state on initialization', async () => {
      // Test implementation
    });
  });
});

// Use realistic test data
const testAgentConfig: AgentConfig = {
  name: 'test-agent',
  goal: 'Testing agent functionality'
};
```

## Documentation Standards

### Docstrings

```typescript
/**
 * Creates a new task with the specified configuration.
 * 
 * Tasks are atomic units of work that can execute independently
 * or depend on other tasks.
 * 
 * @param config - Task configuration object
 * @returns A new Task instance
 * 
 * @example
 * ```typescript
 * const task = new Task({
 *   id: 'fetch-data',
 *   description: 'Fetch data from API',
 *   execute: async (ctx) => {
 *     return await fetch('https://api.example.com/data');
 *   }
 * });
 * ```
 */
function createTask(config: TaskConfig): Task {
  // Implementation
}
```

### README Updates

When adding features, update relevant documentation:
- `QUICKSTART.md` — Update for new users
- `ARCHITECTURE.md` — Update for architectural changes
- `API_REFERENCE.md` — Update for API changes

## Community

### Code of Conduct

This project follows our [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold this code.

### Getting Help

- **Issues** — For bug reports and feature requests
- **Discussions** — For questions and community discussion
- **Email** — For sensitive matters: team@desmond.digital

## Recognition

Contributors are recognized in:
- [CONTRIBUTORS.md](CONTRIBUTORS.md)
- Release notes
- Project documentation

---

## Quick Reference

```bash
# Setup
git clone https://github.com/desmond-digital/ff-terminal.git
cd ff-terminal
npm install
npm test

# Create branch
git checkout -b feature/your-feature

# Make changes, then
npm run lint
npm test

# Commit and push
git add .
git commit -m "feat: description"
git push origin feature/your-feature

# Open PR on GitHub
```

Thank you for contributing to FF-Terminal!

---

*FF-Terminal: autonomous by architecture, not by accident.*
