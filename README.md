# agent-instructions

A standing instruction file for AI coding agents (Claude Code, ChatGPT/Codex and others): how to answer, how to source facts, how to change code, what never to do without asking, and **how to route subagent work to the cheapest model that does it well**.

## Files

| File | For |
|---|---|
| [`CLAUDE.md`](CLAUDE.md) | Claude Code. Put it in your project root or in `~/.claude/CLAUDE.md` for all projects. |
| [`AGENTS.md`](AGENTS.md) | Codex and other agents that read `AGENTS.md`. Same content as `CLAUDE.md`. |
| [`chatgpt/custom-instructions.md`](chatgpt/custom-instructions.md) | ChatGPT: full text for Projects/GPTs, condensed text for Custom Instructions. |
| [`examples/jonata.md`](examples/jonata.md) | A filled-in personal version (Lovable/Hostinger deploy, Brazilian Portuguese). |

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
- If you cap subagents at a lower tier (for cost), delete the tiers you don't allow and let the main thread handle that work. The author runs with subagents capped at Sonnet locally; the file keeps all four tiers because that's the general case.
- Plain ChatGPT chat doesn't spawn subagents, so the condensed ChatGPT version leaves §8 out.

## Credits

§3 and §4 (Think before acting, Simplicity first, Surgical changes, Goal-driven execution) are adapted from [multica-ai/andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) (MIT), which is derived from Andrej Karpathy's observations on LLM coding pitfalls.

## Em português

Arquivo de instruções padrão para agentes de IA (Claude Code, ChatGPT/Codex). Copie o `CLAUDE.md` (ou `AGENTS.md`) para a raiz do projeto, ajuste as linhas marcadas `[ADAPT]` (idioma, plataforma de deploy, nomes de modelo) e pronto. Para o ChatGPT, veja `chatgpt/custom-instructions.md`.

## License

MIT. See [LICENSE](LICENSE).
