# Agent team

For Mona's Project Pulse dashboard, I will use a four-agent custom team orchestrated through GitHub Copilot CLI in a Codespace.

- Planner — Model: Claude Opus 5.5 (copilot). Responsibility: research the repository and relevant docs, identify edge cases and dependencies, and produce a practical implementation plan for the dashboard work. Definition: `.github/agents/planner.agent.md`.
- Orchestrator — Model: Claude Opus 5.5 (copilot). Responsibility: break the plan into phases, delegate work to the specialist agents, keep track of file ownership, and verify that the integrated result fits together cleanly. Definition: `.github/agents/orchestrator.agent.md`.
- Coder — Model: GPT-5.5 (copilot). Responsibility: implement the application logic, fix bugs, and handle runnable app setup such as launch configuration when needed for Project Pulse. Definition: `.github/agents/coder.agent.md`.
- Designer — Model: Gemini 3.7 Flash (copilot). Responsibility: shape the user experience, dashboard layout, accessibility, interaction flow, and visual polish for the Project Pulse frontend. Definition: `.github/agents/designer.agent.md`.

This team is coordinated with GitHub Copilot CLI in a Codespace so the planner can propose work, the orchestrator can assign it, and the coder/designer can work in parallel within their scoped responsibilities.
