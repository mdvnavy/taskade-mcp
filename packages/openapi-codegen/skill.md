# `@taskade/mcp-openapi-codegen` Skill Guide

Follow these steps to generate MCP tools from an OpenAPI 3.0+ spec and wire them into an OpenClaw-friendly server.

## Core API
- Import: `import { codegen } from '@taskade/mcp-openapi-codegen';`
- Call: `await codegen({ path, document, normalizeResponse?, url?, headers?, fetch? });`
  - `path`: output file for generated tools (e.g., `src/tools.generated.ts`).
  - `document`: the dereferenced OpenAPI document (use `@readme/openapi-parser` `dereference`).
  - `url`: base URL for all requests (e.g., `https://www.taskade.com/api/v1`).
  - `headers`: shared headers such as auth tokens; prefer env-driven injection in the consuming server.
  - `fetch`: optional fetch implementation (e.g., `node-fetch`) when the runtime lacks `fetch`.
  - `normalizeResponse`: map of `{ operationId: (response) => McpContent[] }` to shape LLM-facing output.

## Reference Script (reusable)
```ts
// scripts/generate-openapi-tools.ts
import { dereference } from '@readme/openapi-parser';
import { codegen } from '@taskade/mcp-openapi-codegen';

const document = await dereference('taskade-public.yaml');

await codegen({
  path: 'src/tools.generated.ts',
  document,
  url: 'https://www.taskade.com/api/v1',
  // headers: { Authorization: `Bearer ${process.env.API_TOKEN}` },
  // normalizeResponse: { folderProjectsGet: (res) => ({ content: [{ type: 'text', text: JSON.stringify(res) }] }) },
});
```
- Run with `npx tsx scripts/generate-openapi-tools.ts`.
- Check generated file into source control when the API surface should be stable.

## Best Practices for OpenClaw Agents
- Keep operationIds stable; if the spec lacks them, add clear, verb-based ids before generation.
- Normalize responses for high-traffic endpoints to return concise, actionable text instead of raw JSON dumps.
- Do not embed secrets in generated code; pass tokens via env vars when wiring `setupTools` in the MCP server.
- After generation, list exported tool names to the user and confirm any destructive endpoints before exposure.
- When specs include large response schemas, prefer summarizing in `normalizeResponse` to keep LLM context small.

## Wiring Into an MCP Server
- Import generated `setupTools` into your server and pass configuration (base URL, headers, fetch).
- Pair the generated tools with the Taskade MCP server or any custom server, then document the tool names in the agent prompt the OpenClaw runtime uses.
- If the consuming agent needs pagination or rate limits, add guidance in that agent’s prompt before using the tools.

