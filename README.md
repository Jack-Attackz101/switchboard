# Switchboard

One chat window for every AI agent you use.

Claude Code, Codex, Gemini, the Anthropic and OpenAI APIs, the bots that only
live in Slack, and Aside itself, all in a single iMessage-style app running on
your own machine. Make a group, drop three of them in it, and ask one question.

Name is a placeholder. Rename it whenever.

## Run it

```
node server.mjs
```

Then open http://localhost:4173. Node 18 or newer, no dependencies, no build
step, nothing installed.

On first run it copies `agents.example.json` to `agents.json`. Edit that file to
add, remove or reconfigure people. New agents added to the example in a later
update get merged into your `agents.json` automatically, and retired ones get
removed, so you never silently run an old roster.

## What works with no API key

Plenty, which is the point.

| Agent | How it runs | Needs |
| --- | --- | --- |
| ASIDE | `claude` CLI plus your real Aside memory | your existing claude login |
| CLAUDE CODE | `claude` CLI | your existing claude login |
| CODEX | `codex` CLI | your existing codex login |
| ECHO | local stub | nothing |
| VIKTOR and the Slack crew | posts in Slack, reads the thread | `SLACK_TOKEN` |
| GPT, CLAUDE CHAT | HTTP API | an API key |

Anything needing a key you do not have is switched off in the config rather
than sitting there failing. Flip `enabled` to `true` when you have one.

```
export SLACK_TOKEN=xoxb-...          # for Viktor and the Relevance agents
export ANTHROPIC_API_KEY=sk-ant-...  # only for the api-kind agents
export OPENAI_API_KEY=sk-...
```

## Adapters

- **cli** spawns the agent's command per message and resumes its session, so
  each conversation keeps its context.
- **api** talks to Anthropic or OpenAI directly.
- **slack** is for bots with no HTTP API at all. It posts a message that
  mentions them in a channel, then polls the thread under that message, because
  that is where those bots reply. Keyword routing picks out sub-agents that
  share one Slack app.
- **aside** reads your real Aside memory files off disk and hands them over as
  context, so it already knows your projects.
- **echo** just echoes, for checking the UI works.

## Live activity

While a CLI agent is working, the conversation shows what it is actually doing,
not a spinner.

Claude Code writes each session to `~/.claude/projects/<cwd-with-dashes>/<id>.jsonl`
and every assistant line names the tool it just used. The server tails that file
and turns it into a status line: `reading index.html`, `editing server.mjs`,
`running npm test`, `delegating`, `looking up docs`.

Nothing is written and Claude Code is not modified. Anthropic documents this
file as internal and says the shape can change between releases, so every read
is defensive and falls back to plain "working" if it cannot parse.

Turn it off per agent with `"cli": { "watchTranscript": false }`.

## In the app

- **Groups.** Hit `+`, name it, tick the agents. Everyone in the group gets the
  message and answers in the same thread, labelled by name.
- **Everyone.** A permanent group with all enabled agents in it.
- **Drag files in** to attach them as text. Up to 6 at a time, 240kb each.
- **Rename** anyone from the conversation header.
- **`Cmd K`** to search, `Enter` to send, `Shift Enter` for a newline, `Esc` to
  clear.
- Follows your system light or dark setting.

## Files

```
server.mjs            the whole backend, zero dependencies
index.html            the whole frontend, zero dependencies
agents.example.json   the roster that seeds agents.json
agents.json           yours, created on first run, gitignored
```

## Not here

No accounts, no telemetry, no cloud. Nothing leaves your machine except the
calls the agents themselves make. `agents.json` and `.mjs` files are never
served over HTTP, so your keys stay put.

