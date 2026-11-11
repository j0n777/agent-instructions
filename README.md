# agent-instructions

A standing instruction file for AI coding agents (Claude Code, ChatGPT/Codex and others): how to answer, how to source facts, how to change code, what never to do without asking, and **how to route subagent work to the cheapest model that does it well**.

## Install

In your project root (macOS/Linux/Git Bash):

```bash
curl -fsSL https://raw.githubusercontent.com/j0n777/agent-instructions/main/CLAUDE.md >> CLAUDE.md
```

Windows PowerShell:

```powershell
(Invoke-WebRequest -UseBasicParsing https://raw.githubusercontent.com/j0n777/agent-instructions/main/CLAUDE.md).Content | Add-Content -Encoding utf8 CLAUDE.md
```

Both **append**, so an existing `CLAUDE.md` keeps its content; review the result and remove anything that conflicts. For all projects, run it inside `~/.claude/` instead. For Codex, swap `CLAUDE.md` for `AGENTS.md` in both places.

See [`EXAMPLES.md`](EXAMPLES.md) for before/after cases.

## Files

| File | For |
|---|---|
| [`CLAUDE.md`](CLAUDE.md) | Claude Code. Put it in your project root or in `~/.claude/CLAUDE.md` for all projects. |
| [`AGENTS.md`](AGENTS.md) | Codex and other agents that read `AGENTS.md`. Same content as `CLAUDE.md`. |
| [`EXAMPLES.md`](EXAMPLES.md) | Before/after cases: routing, escalation, briefs, sources, surgical changes, irreversible actions. |
| [`claude-code/agents/`](claude-code/agents/) | Claude Code subagents pinned to Haiku: `explore.md` and `log-reader.md`. |
| [`chatgpt/custom-instructions.md`](chatgpt/custom-instructions.md) | ChatGPT: full text for Projects/GPTs, condensed text for Custom Instructions. |

Lines marked `[ADAPT]` are personal preferences: language, deploy platform, model names. Change them; the rest is meant to work as is.

## What's in it

- **§0 Priorities**: an explicit order for when rules conflict (safety first, cost last).
- **§2 Facts and sources**: no claim without a source checked in the session; say "not confirmed" instead of guessing.
- **§3–4 Think before acting / writing code**: adapted from Andrej Karpathy's observations on LLM coding pitfalls (see Credits).
- **§5 Git**: PR discipline and pacing to stay clear of GitHub's secondary rate limits.
- **§7 Irreversible actions**: migrations, deletions, DNS, env vars and payments wait for an explicit OK.
- **§8 Model routing**: four tiers (Haiku → Sonnet → Opus → Fable), rules for when to move up a tier, a mandatory brief template for subagents, and a review loop with escalation.

## Model routing notes

- The tier names are Claude models. On another provider, map each tier to an equivalent model of similar cost and capability.
- If you cap subagents at a lower tier (for cost), delete the tiers you don't allow and let the main thread handle that work.
- The tier uses and the escalation order (verify → raise effort → move up a model) follow Anthropic's guide [Which Claude model should you build with?](https://claude.dev/blog/which-claude-model-should-you-build-with/) (Nov 9, 2026). Model names and prices change; check that post and the [model docs](https://platform.claude.com/docs/en/models/overview) before relying on them.

### Claude Code subagents

[`claude-code/agents/`](claude-code/agents/) has two subagent definitions that pin cheap work to Haiku:

- `explore.md`: overrides the built-in Explore subagent, which (per the guide above) runs on your session's model, not on Haiku.
- `log-reader.md`: reads test output and logs and reports each failure.

Copy them to `.claude/agents/` (one project) or `~/.claude/agents/` (all projects). `CLAUDE_CODE_SUBAGENT_MODEL` sets the default model for subagents that don't name one.
- Plain ChatGPT chat doesn't spawn subagents, so the condensed ChatGPT version leaves §8 out.

## Credits

§3 and §4 (Think before acting, Simplicity first, Surgical changes, Goal-driven execution) are adapted from [multica-ai/andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) (MIT), which is derived from Andrej Karpathy's observations on LLM coding pitfalls.

## Em português

Arquivo de instruções padrão para agentes de IA (Claude Code, ChatGPT/Codex). Copie o `CLAUDE.md` (ou `AGENTS.md`) para a raiz do projeto, ajuste as linhas marcadas `[ADAPT]` (idioma, plataforma de deploy, nomes de modelo) e pronto. Para o ChatGPT, veja `chatgpt/custom-instructions.md`.

## License

MIT. See [LICENSE](LICENSE).
