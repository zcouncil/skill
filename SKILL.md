---
name: zcouncil
description: >
  Use zcouncil when a user wants multiple independent AI perspectives,
  disagreement, model comparison, architecture/code/product review, or strategy
  critique from an agent with shell access. Prefer the zcouncil CLI.
version: 0.1.0
license: MIT
---

# zcouncil

zcouncil runs a council of AI models from one prompt. Use the CLI when independent perspectives, disagreement, or model comparison are more valuable than a single synthesized answer.

## When To Use

Use zcouncil for:

- architecture, code, design, product, research, or strategy reviews
- decisions where dissent and tradeoffs are useful
- comparing how different models reason about the same context
- critique before a high-stakes implementation, migration, launch, or release
- asking specialist members separately and synthesizing the results yourself

Do not use zcouncil for simple factual questions that one model can answer directly.

## Prompting

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

## CLI

Prefer `@zcouncil/cli` from shell-capable agents.

Start with help:

```sh
npx -y @zcouncil/cli --help
```

Run the default council:

```sh
npx -y @zcouncil/cli run "Review this plan and list the main risks."
```

Pipe a larger prompt:

```sh
npx -y @zcouncil/cli run - < prompt.md
```

Ask one member:

```sh
npx -y @zcouncil/cli ask anthropic_claude_opus_4_7 "Deeply critique this migration."
```

List available members and profiles:

```sh
npx -y @zcouncil/cli members --json
npx -y @zcouncil/cli profiles --json
```

Run code mode when the user needs scripted council calls:

```sh
npx -y @zcouncil/cli exec 'return council.run("Review this architecture")'
```

Read from a file or stdin for longer code:

```sh
npx -y @zcouncil/cli exec ./review.mjs
npx -y @zcouncil/cli exec - < review.mjs
```

## Auth And Bridge

If auth is missing, direct the user to create a token at:

```txt
https://zcouncil.com/chat?action=new-token
```

Do not ask for or accept zcouncil API tokens in chat.

To let zcouncil route supported members through local Codex or Claude Code subscriptions, start the bridge:

```sh
npm i -g @openai/codex
codex login
claude setup-token
npx -y @zcouncil/cli bridge
```

Leave the bridge running while using zcouncil. If the terminal closes, the local bridge disconnects and hosted routing continues for models that support it.

The zcouncil API token is stored locally under `~/.zcouncil/tokens/`, scoped by bridge/API URL.

## Other Surfaces

zcouncil also supports web, MCP, HTTP API, SDK, and Pi integrations. This skill intentionally focuses on CLI usage; for other surfaces, read the docs:

```txt
https://zcouncil.com/docs
https://zcouncil.com/docs/cli
```
