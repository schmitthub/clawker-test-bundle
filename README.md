# clawker-test-bundle

A working example [clawker](https://github.com/schmitthub/clawker) bundle —
fork this repo to build your own. It ships three harnesses:

| Component | Address | What it does |
|-----------|---------|--------------|
| `harnesses/claude` | `schmitthub.test-bundle.claude` | [Claude Code](https://code.claude.com) via the canonical installer (version resolved from npm `@anthropic-ai/claude-code`). Declares the `node` stack, persists `~/.claude` as a volume, seeds statusline/settings on first boot, stages host `~/.claude` state (settings allowlist, agents, skills, commands, plugin registry with path rewrites), and floors egress at the Anthropic API/OAuth/telemetry domains |
| `harnesses/codex` | `schmitthub.test-bundle.codex` | [Codex CLI](https://github.com/openai/codex) via the canonical installer (version resolved from GitHub release tags `rust-v*`). Self-contained binary — no stack. Persists `~/.codex` as a volume, stages host global `AGENTS.md` + prompts, and floors egress at the OpenAI API/auth domains with `chatgpt.com` path-scoped to the codex backend |
| `harnesses/opencode` | `schmitthub.test-bundle.opencode` | [OpenCode](https://opencode.ai) via the canonical installer (version resolved from npm `opencode-ai`). Persists `~/.config/opencode` + `~/.local/share/opencode` as volumes, stages the host's global `AGENTS.md`, and floors egress at `models.dev` + `api.anthropic.com` |

The `claude` and `codex` harnesses mirror clawker's embedded floor harnesses —
they are the reference implementations, kept here as complete worked examples
of every harness surface (stacks, volumes, seeds + assets, staging with
`json_keys`/`json_rewrites`, path-scoped egress, both npm and github-release
version resolvers). The `opencode` harness shows the minimal shape.

Declare and build:

```yaml
bundles:
  - url: https://github.com/schmitthub/clawker-test-bundle.git
    ref: v0.3.0
```

```bash
clawker bundle install
clawker build -t schmitthub.test-bundle.codex
```

## Anatomy of a harness

```
harnesses/<name>/
├── harness.yaml             # manifest: version resolver, stacks, volumes,
│                            # seeds, staging, egress floor
├── Dockerfile.harness.tmpl  # template blocks composed into clawker's
│                            # master Dockerfile (install steps, ENV, CMD)
└── assets/                  # optional files staged into the build context
                             # (referenced by seeds: and COPY)
```

Planned additions (ongoing): a `pi` harness and the remaining embedded
components.
