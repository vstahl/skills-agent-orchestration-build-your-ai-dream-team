# Agent team

I am using GitHub Copilot CLI in a Codespace to orchestrate this team of custom agents to build Mona's Project Pulse dashboard.

| Agent | Target model | Responsibility | Definition |
|-------|--------------|----------------|------------|
| Orchestrator | Claude Opus 4.7 (copilot) | Breaks down the request, delegates to specialists with explicit file scopes, runs work in parallel or sequentially, and verifies the integrated result. Does not implement. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository and docs, then returns an ordered plan with file assignments, dependencies, edge cases, and validation expectations. Writes no code. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements code and logic within assigned files, including runnable app support such as `.vscode/launch.json`, and validates changes. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.1 Pro (copilot) | Handles UI/UX, accessibility, and styling: a polished, responsive dashboard with project cards, status badges, and clear priority treatment. | `.github/agents/designer.agent.md` |

All agents are forbidden from staging, committing, or pushing; I control git through Copilot CLI prompts.
