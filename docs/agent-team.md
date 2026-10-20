# Agent team

The custom agent team for Mona's Project Pulse dashboard is:

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| **Planner** | Claude Opus 5.5 (copilot) | Researches the repository, dependencies, documentation, and edge cases, then provides an implementation plan with file assignments and validation expectations. Does not write code. | `.github/agents/planner.agent.md` |
| **Designer** | Gemini 3.7 Flash (copilot) | Shapes the dashboard's user experience, accessibility, information hierarchy, interaction flow, responsive layout, and visual styling. | `.github/agents/designer.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements application logic and assigned runnable-app support, following repository patterns and validating changes. | `.github/agents/coder.agent.md` |
| **Orchestrator** | Claude Opus 5.5 (copilot) | Coordinates Planner, Designer, and Coder: plans phases, assigns non-overlapping file scopes, manages dependencies, and verifies the integrated result. Does not implement the work directly. | `.github/agents/orchestrator.agent.md` |

I am using **GitHub Copilot CLI in a Codespace** to orchestrate the work with this team.
