# OpenClaw Agent Playbook for `@taskade/mcp-openapi-codegen`

Use this when an OpenClaw agent needs to turn any OpenAPI 3.0+ spec into MCP tools for downstream servers.

## Purpose
- Generate MCP tool definitions from an OpenAPI document so OpenClaw can call external APIs safely.
- Keep the generation script in-repo for repeatable runs; avoid ad-hoc manual edits to generated files.

## Setup
- Install dev deps in the target project: `npm install --save-dev @taskade/mcp-openapi-codegen @readme/openapi-parser`.
- Create a small generator script (see `skill.md`) instead of running codegen inline in prompts.
- Inputs: path/URL to the OpenAPI document, output path for generated tools, optional headers/fetch override.

## Guardrails
- Only write generated files inside the repository workspace; never outside user-owned paths.
- If the spec references private hosts, remind the user and avoid leaking URLs in logs.
- Prefer explicit base URLs and auth headers instead of embedding secrets in code.

## Flow
1) Validate the spec is accessible (local file or URL the runtime can read).
2) Run the generator script to produce `tools.generated.ts` (or a user-chosen path).
3) Wire `setupTools` into the MCP server and confirm tool names with the user.
4) Hand off to downstream skill guidance in `skill.md` for options and normalization patterns.

