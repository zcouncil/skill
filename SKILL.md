---
name: zcouncil
description: >
  Use zcouncil when a user wants multiple independent AI perspectives, model
  comparison, dissent, architecture/code/product review, product or strategy
  critique, or help integrating zcouncil through Pi, MCP, the CLI, or the
  TypeScript SDK.
version: 0.1.0
license: MIT
---

# zcouncil

zcouncil runs a council of AI models from one prompt. Use it when independent perspectives, disagreement, or model comparison are more valuable than a single synthesized answer.

## When to use zcouncil

Use zcouncil for:

- architecture, code, design, product, research, or strategy reviews
- decisions where dissent and tradeoffs are useful
- comparing how different models reason about the same context
- critique before a high-stakes implementation, migration, launch, or release
- asking specialist members separately and synthesizing the results yourself

Do not use zcouncil for simple factual questions that one model can answer directly.

## Prompting guidance

Council prompts work best when they are neutral and complete.

Do:

- State the decision or question clearly.
- Include relevant context, constraints, files, logs, or excerpts.
- Ask for tradeoffs, risks, dissent, and recommended next steps.
- Tell members what output shape you want.

Avoid:

- Leading members toward your preferred answer.
- Hiding context that weakens one option.
- Asking council members merely to agree with a plan.
- Sending secrets, credentials, customer data, or regulated data unless the user explicitly authorizes it and the selected zcouncil deployment is appropriate for that data.

## Using zcouncil from an agent

Prefer the integration already available in the current environment.

### Pi

If the current agent is Pi and `@zcouncil/pi` is installed, use the `zcouncil` tool for council requests.

Default tool call:

```txt
zcouncil({})
```

That asks the default council about the latest user message. Results arrive asynchronously as follow-up context messages.

If the Pi package is not installed, tell the user to run:

```sh
pi install npm:@zcouncil/pi
```

Then, inside Pi, the user should run:

```txt
/zcouncil login
```

Do not ask for or accept zcouncil API tokens in chat or tool arguments.

### MCP

Use the hosted MCP server when the current client has zcouncil configured as an MCP tool.

Endpoint for humans to configure outside chat:

```txt
https://api.zcouncil.com/mcp
```

The MCP server exposes one main tool:

- `run` — runs sandboxed JavaScript council code.

Example MCP payload:

```json
{
  "code": "return council.run(`Review this architecture and recommend next steps.`)"
}
```

MCP scripts do not automatically receive the caller's chat transcript. Include relevant context in the string passed to `council.run()` or `council.ask()`.

Available script globals:

- `council.run(input)` — ask the default/Auto council.
- `council.ask(memberId, input, description)` — ask one member.
- `council.members()` — list valid member IDs.
- `council.profiles()` — list saved council profiles.
- `ui.status(text)` — emit a short visible progress update.
- `orchestrator.checkpoint({ description, prompt })` — visible intermediate checkpoint for multi-step hosted MCP flows.
- Native `Promise.all()` / `Promise.allSettled()` — run member calls concurrently.

Example: ask two members concurrently.

```json
{
  "code": "const input = `Should we move this worker to a Durable Object?`;\nconst [architecture, rollout] = await Promise.all([\n  council.ask('anthropic_claude_opus_4_7', input, 'Review the architecture deeply'),\n  council.ask('openai_gpt_5_4_mini', input, 'Suggest practical rollout steps')\n]);\nreturn { architecture, rollout };"
}
```

The sandbox has no filesystem, secrets, outbound network, imports, `eval`, `Function`, or `globalThis` access. Return small JSON-serializable outputs or notes.

### CLI

Use `@zcouncil/cli` when the current environment has shell access but no MCP/Pi tool, or when the user wants local Codex/Claude Code subscriptions to power supported council members.

Install/run:

```sh
npx -y @zcouncil/cli --help
```

Direct council calls after the user has configured auth:

```sh
npx -y @zcouncil/cli run "Review this plan"
npx -y @zcouncil/cli run - < prompt.md
npx -y @zcouncil/cli ask anthropic_claude_opus_4_7 "Critique this migration"
npx -y @zcouncil/cli members --json
npx -y @zcouncil/cli profiles --json
```

Local bridge setup:

```sh
npm i -g @openai/codex
codex login
claude setup-token
npx -y @zcouncil/cli bridge
```

Leave the bridge running while using zcouncil. If the terminal closes, the local bridge disconnects and hosted routing continues for models that support it.

The zcouncil API token is stored locally under `~/.zcouncil/tokens/`, scoped by bridge/API URL. Do not ask users to paste tokens into agent chat.

### TypeScript SDK

Use `@zcouncil/sdk` when building zcouncil into application code or scripts.

Install:

```sh
npm install @zcouncil/sdk
```

Basic usage:

```ts
import { ZCouncilClient } from "@zcouncil/sdk"

const zcouncil = new ZCouncilClient({
  token: process.env.ZCOUNCIL_TOKEN!,
})

const answer = await zcouncil.run("Review this architecture")
```

API:

```ts
await zcouncil.run(input)
await zcouncil.ask(memberId, input)
await zcouncil.members()
await zcouncil.profiles()
```

Use normal JavaScript concurrency to ask multiple members:

```ts
const [critique, rollout] = await Promise.all([
  zcouncil.ask("anthropic_claude_opus_4_7", "Deeply critique this plan"),
  zcouncil.ask("openai_gpt_5_4_mini", "Suggest practical rollout steps"),
])
```

Never hardcode tokens in source files. Use environment variables or the user-approved secret store for the runtime.

## Quick decision guide

- Use the web app for interactive council chats.
- Use Pi's `zcouncil` tool inside Pi.
- Use MCP when an MCP client should consult zcouncil as a tool.
- Use `@zcouncil/cli` when an agent/script has shell access but no MCP setup, or when the user wants local Codex/Claude Code subscriptions to power supported members.
- Use `@zcouncil/sdk` when building zcouncil into a TypeScript app, server, or script.
