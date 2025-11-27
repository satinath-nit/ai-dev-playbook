# Write README

> Generate a professional, comprehensive README for your project that helps users understand, install, and use your software.

## When to Use

- Starting a new project and need a README
- Your existing README is outdated or incomplete
- Open-sourcing an internal project
- Improving documentation for better adoption

## The Prompt

```text
Generate a professional README.md for my project.

**Project Information**:
- Name: [PROJECT_NAME]
- Description: [DESCRIPTION]
- Language/Framework: [TECH_STACK]
- Target Audience: [AUDIENCE]

**Key Features**:
[FEATURES]

**Installation Requirements**:
[REQUIREMENTS]

**Usage Example** (if available):
```[LANGUAGE]
[USAGE_EXAMPLE]
```

**Additional Sections to Include**:
- [ ] Badges (build status, version, license)
- [ ] Table of Contents
- [ ] Screenshots/GIFs
- [ ] API Documentation
- [ ] Configuration Options
- [ ] Contributing Guidelines
- [ ] License
- [ ] Acknowledgments

Please create a README that:
1. Clearly explains what the project does in the first paragraph
2. Gets users up and running quickly
3. Provides enough detail for both beginners and advanced users
4. Follows README best practices
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `[PROJECT_NAME]` | Name of your project | `FastCache`, `react-data-grid` |
| `[DESCRIPTION]` | What the project does | "A high-performance caching library" |
| `[TECH_STACK]` | Technologies used | `Python 3.9+`, `Node.js/TypeScript` |
| `[AUDIENCE]` | Who will use this | "Backend developers", "Data scientists" |
| `[FEATURES]` | Key features list | "- In-memory caching\n- TTL support" |
| `[REQUIREMENTS]` | Prerequisites | "Python 3.9+, Redis (optional)" |
| `[USAGE_EXAMPLE]` | Basic code example | Your hello world example |

## Example

### Input

```text
Generate a professional README.md for my project.

**Project Information**:
- Name: TaskFlow
- Description: A lightweight task queue library for Python with async support
- Language/Framework: Python 3.9+
- Target Audience: Python developers who need background job processing

**Key Features**:
- Simple decorator-based API
- Async/await support
- Multiple backend support (Redis, PostgreSQL, In-memory)
- Automatic retries with exponential backoff
- Task scheduling (cron-like)
- Web dashboard for monitoring

**Installation Requirements**:
- Python 3.9 or higher
- Redis (optional, for distributed mode)

**Usage Example**:
```python
from taskflow import task, TaskFlow

app = TaskFlow()

@task
async def send_email(to: str, subject: str):
    # Your email logic here
    pass

# Queue a task
await send_email.delay("user@example.com", "Welcome!")
```

**Additional Sections to Include**:
- [x] Badges
- [x] Table of Contents
- [x] Configuration Options
- [x] Contributing Guidelines
- [x] License
```

### Expected Output

```markdown
# TaskFlow

[![PyPI version](https://badge.fury.io/py/taskflow.svg)](https://badge.fury.io/py/taskflow)
[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Tests](https://github.com/username/taskflow/workflows/tests/badge.svg)](https://github.com/username/taskflow/actions)

A lightweight, async-first task queue library for Python. Process background jobs with a simple decorator-based API.

## Features

- **Simple API** - Decorator-based task definition
- **Async Native** - Built for async/await from the ground up
- **Multiple Backends** - Redis, PostgreSQL, or in-memory
- **Automatic Retries** - Exponential backoff out of the box
- **Scheduling** - Cron-like task scheduling
- **Monitoring** - Built-in web dashboard

## Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Backends](#backends)
- [Task Options](#task-options)
- [Scheduling](#scheduling)
- [Monitoring](#monitoring)
- [Contributing](#contributing)
- [License](#license)

## Installation

```bash
pip install taskflow
```

With Redis support:
```bash
pip install taskflow[redis]
```

With PostgreSQL support:
```bash
pip install taskflow[postgres]
```

## Quick Start

1. Define a task:

```python
from taskflow import task, TaskFlow

app = TaskFlow()

@task
async def send_email(to: str, subject: str, body: str):
    # Your email sending logic
    print(f"Sending email to {to}")
```

2. Queue the task:

```python
# Queue for immediate execution
await send_email.delay("user@example.com", "Welcome!", "Thanks for signing up")

# Queue with delay
await send_email.delay("user@example.com", "Reminder", "...", delay=3600)
```

3. Start the worker:

```bash
taskflow worker
```

## Configuration

Configure TaskFlow using environment variables or a config file:

```python
from taskflow import TaskFlow

app = TaskFlow(
    broker_url="redis://localhost:6379/0",
    result_backend="redis://localhost:6379/1",
    task_default_retry_limit=3,
    task_default_retry_delay=60,
)
```

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `TASKFLOW_BROKER_URL` | Message broker URL | `memory://` |
| `TASKFLOW_RESULT_BACKEND` | Result storage URL | `memory://` |
| `TASKFLOW_RETRY_LIMIT` | Default retry attempts | `3` |
| `TASKFLOW_RETRY_DELAY` | Delay between retries (seconds) | `60` |

## Backends

### In-Memory (Development)

```python
app = TaskFlow(broker_url="memory://")
```

### Redis (Recommended for Production)

```python
app = TaskFlow(broker_url="redis://localhost:6379/0")
```

### PostgreSQL

```python
app = TaskFlow(broker_url="postgresql://user:pass@localhost/taskflow")
```

## Task Options

```python
@task(
    retry_limit=5,
    retry_delay=30,
    timeout=300,
    queue="high-priority"
)
async def important_task():
    pass
```

## Scheduling

Schedule recurring tasks with cron syntax:

```python
@task
@schedule("0 9 * * *")  # Every day at 9 AM
async def daily_report():
    pass
```

## Monitoring

Start the web dashboard:

```bash
taskflow dashboard --port 8080
```

Access at `http://localhost:8080` to view:
- Active tasks
- Failed tasks and errors
- Task history
- Worker status

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Inspired by Celery and RQ
- Built with asyncio and aioredis
```

## Tips & Variations

### Tool-Specific Notes

- **GitHub Copilot**: Works well for generating sections incrementally
- **Cursor**: Can generate README from codebase context
- **Claude/ChatGPT**: Best for comprehensive README generation

### Common Pitfalls

- **Too much detail upfront**: Lead with the value proposition
- **Missing installation steps**: Always test your install instructions
- **Outdated examples**: Keep code examples in sync with your API

### Follow-up Prompts

To add a specific section:
```text
Add a "Troubleshooting" section covering common issues like [ISSUE_1] and [ISSUE_2].
```

To improve an existing README:
```text
Here's my current README. Please improve it by adding [MISSING_SECTIONS] and making the Quick Start clearer:
[CURRENT_README]
```

## Related Playbooks

- [Generate API Docs](generate-api-docs.md)
- [Document Architecture](document-architecture.md)
- [Add Code Comments](add-code-comments.md)
