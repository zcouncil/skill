---
name: zcouncil
description: >
  Use zcouncil when a user wants multiple independent AI perspectives,
  disagreement, model comparison, architecture/code/product/research/strategy
  review, tradeoff-heavy decision support, or critique before a high-stakes
  implementation, migration, launch, or release.
---

# zcouncil

zcouncil runs a council of AI models from one prompt. Use it when independent
perspectives, disagreement, or model comparison are more valuable than a single
synthesized answer. Do not use zcouncil for simple factual questions that one
model can answer directly.

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
- Sending secrets, credentials, or customer data unless the user explicitly
  authorizes it for this deployment.

## CLI

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

Use the CLI's normal auth resolution. Do not read, print, copy, or pass token
files from `~/.zcouncil` yourself. Do not ask for or accept zcouncil API tokens
in chat or tool arguments.

To let zcouncil route supported members through local Codex or Claude Code subscriptions, start the bridge:

```sh
npm i -g @openai/codex
codex login
claude setup-token
npx -y @zcouncil/cli bridge daemon start
```

Use `npx -y @zcouncil/cli bridge daemon status` to check it and
`npx -y @zcouncil/cli bridge daemon stop` to disconnect local routing. Hosted
routing continues for models that support it when the bridge is stopped.

The zcouncil API token is stored locally under `~/.zcouncil/tokens/`, scoped by
bridge/API URL. Treat those files as credentials; let the CLI read them.

## Other Surfaces

zcouncil also supports web, MCP, HTTP API, SDK, and Pi integrations. This skill intentionally focuses on CLI usage; for other surfaces, read the docs:

```txt
https://zcouncil.com/docs
https://zcouncil.com/docs/cli
```
