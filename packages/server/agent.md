# Taskade MCP Agent Playbook (OpenClaw)

This file guides an OpenClaw agent that is allowed to use the Taskade MCP server. Pair it with `skill.md` for tool-by-tool details and guardrails.

## Connection
- Use the MCP server command: `npx -y @taskade/mcp-server` with env `TASKADE_API_KEY=<personal access token>`.
- For SSE/HTTP mode (when OpenClaw needs HTTP): `TASKADE_API_KEY=<token> npx @taskade/mcp-server --http` then connect to `http://localhost:3000/sse?access_token=<token>`.
- Keep the API key scoped to a non-admin account when possible and never log it.

## Operating Principles
- Start by learning the user’s intent, workspace, and project names before calling tools.
- Prefer read tools first (list workspaces/projects/tasks) to confirm targets, then write operations.
- Avoid destructive calls (delete, overwrite) unless the user explicitly requested them; otherwise suggest a safer alternative.
- Include Taskade resource links in responses when available to help the user verify changes.
- Batch related updates (e.g., create multiple tasks in one `taskCreate` call) when it lowers tool churn without losing clarity.

## Skill Map
- All tools and decision rules live in `./skill.md`.
- Follow the category playbooks there to decide which tool to call and with which required inputs.

## Common Flows
- **Plan a project:** list workspaces → pick project or create one → add tasks → set due dates/assignees.
- **Triage tasks:** list overdue tasks → move or update status → confirm completion.
- **Publish an agent:** create or update an agent → enable public access → return the share link.

## Error Handling
- If a tool fails, surface the HTTP status/message, restate what was attempted, and propose the next safest step (usually re-list to verify current state).
- When pagination exists, request the next page only if needed for the user’s goal.

