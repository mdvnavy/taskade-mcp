# Taskade MCP Skill Guide

Use this alongside `agent.md`. Pick the smallest tool that completes the user’s request, confirm targets by name/ID, and avoid destructive calls without explicit consent.

## Workspaces and Projects
- `workspacesGet`: enumerate workspaces before creating or moving items; return names and ids.
- `workspaceFoldersGet`: get folders to locate the right context for projects or agents.
- `projectGet` / `projectBlocksGet` / `projectTasksGet`: read project content before editing.
- `projectCreate`, `projectCopy`, `projectFromTemplate`: create projects; prefer templates when user asks for a known blueprint.
- `projectComplete` / `projectRestore`: toggle completion only after confirming intent.
- `projectShareLinkEnable` / `projectShareLinkGet`: enable and return share links; include link in the reply.

## Tasks
- `taskCreate`: create multiple tasks in one call using clear titles; include parent `projectId`.
- `taskPut`, `taskMove`: update or move tasks; restate the target task name before sending.
- `taskComplete` / `taskUncomplete`: only after user confirmation or when fulfilling a stated goal.
- `taskGetDate` / `taskPutDate` / `taskDeleteDate`: manage due dates; include timezone context from the user if available.
- `taskAssigneesGet`, `taskPutAssignees`, `taskDeleteAssignees`: only assign users returned by Taskade; do not guess emails.
- `taskNoteGet` / `taskNotePut` / `taskNoteDelete`: keep notes concise and markdown-friendly.
- `taskFieldsValue*`: custom fields; read first, then update specific fields rather than bulk overwrites.

## AI Agents
- `folderAgentGenerate`: generate an agent from a prompt when speed matters; summarize the prompt you’ll send.
- `folderCreateAgent` / `agentUpdate`: create or edit configuration; echo key settings (name, model, temperature).
- `agentPublicAccessEnable` / `agentPublicGet` / `agentPublicUpdate`: publish agents; always return the public link and visibility status.
- `agentKnowledgeProjectCreate` / `agentKnowledgeMediaCreate` and corresponding remove calls: manage knowledge sources; confirm counts after changes.
- `agentConvosGet` / `agentConvoGet`: use for status or debugging; avoid over-fetching long histories unless requested.

## Templates and Media
- `folderProjectTemplatesGet`: list templates before creating from one; return ids and titles.
- `mediasGet`, `mediaGet`, `mediaDelete`: fetch or clean media; warn before deleting.

## Personal
- `meProjectsGet`: quick way to fetch the user’s projects across workspaces; good for onboarding flows.

## Best Practices
- Always capture and reuse returned ids; avoid string-matching names alone.
- When lists are long, filter by the user’s keywords instead of pulling every item.
- Provide a concise summary of what changed plus actionable links (project/task/agent).
- On 401/403 errors, ask for a fresh `TASKADE_API_KEY` or reduced scope; on 404, re-list the parent collection before retrying.
- For multi-step goals, describe the planned sequence to the user, then execute tools in that order.

