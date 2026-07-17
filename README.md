# clawker-test-bundle

A working example [clawker](https://github.com/schmitthub/clawker) bundle —
fork this repo to build your own. It ships three harnesses and eight
stacks:

| Component | Address | What it does |
|-----------|---------|--------------|
| `harnesses/claude` | `schmitthub.test-bundle.claude` | [Claude Code](https://code.claude.com) via the canonical installer (version resolved from npm `@anthropic-ai/claude-code`). Declares the `node` stack, persists `~/.claude` as a volume, seeds statusline/settings on first boot, stages host `~/.claude` state (settings allowlist, agents, skills, commands, plugin registry with path rewrites), and floors egress at the Anthropic API/OAuth/telemetry domains |
| `harnesses/codex` | `schmitthub.test-bundle.codex` | [Codex CLI](https://github.com/openai/codex) via the canonical installer (version resolved from GitHub release tags `rust-v*`). Self-contained binary — no stack. Persists `~/.codex` as a volume, stages host global `AGENTS.md` + prompts, and floors egress at the OpenAI API/auth domains with `chatgpt.com` path-scoped to the codex backend |
| `harnesses/opencode` | `schmitthub.test-bundle.opencode` | [OpenCode](https://opencode.ai) via the canonical installer (version resolved from npm `opencode-ai`). Persists `~/.config/opencode` + `~/.local/share/opencode` as volumes, stages the host's global `AGENTS.md`, and floors egress at `models.dev` + `api.anthropic.com` |

The `claude` and `codex` harnesses are patterned on clawker's embedded
harnesses — complete worked examples of every harness surface (stacks,
volumes, seeds + assets, staging with `json_keys`/`json_rewrites`,
path-scoped egress, both npm and github-release version resolvers). The
`opencode` harness shows the minimal shape.

The `stacks/` directory ships eight example stacks — `go`, `node`,
`python`, `rust`, `java`, `ruby`, `cpp`, and `dotnet`, addressed as
`schmitthub.test-bundle.<name>` — patterned on clawker's embedded ones.
Together they demonstrate every stack pattern: root-scope and user-scope
fragments (`node` ships both), checksum-verified official tarballs (`go`),
GPG-verified installs (`node`), `curl | sh` installer scripts (`python`,
`rust`, `dotnet`), plain apt toolchains (`java`, `ruby`, `cpp`), shared
world-writable state dirs (`go`'s GOPATH, `ruby`'s GEM_HOME), version-pin
ARGs over floating channels, and the self-guard convention (every fragment
skips itself when the image already provides the tool).

Declare and build:

```yaml
bundles:
  - url: https://github.com/schmitthub/clawker-test-bundle.git
    ref: v0.1.2
```

```bash
clawker bundle install
clawker build -t schmitthub.test-bundle.codex
```

## Anatomy of a harness

```
harnesses/<name>/
├── harness.yaml             # manifest: version resolver, stacks, volumes,
│                            # seeds, staging, managed_prompt, egress floor
├── Dockerfile.harness.tmpl  # template blocks composed into clawker's
│                            # master Dockerfile (install steps, ENV, CMD)
└── assets/                  # optional files staged into the build context
                             # (referenced by seeds: and COPY)
```

## Anatomy of a stack

```
stacks/<name>/
├── stack.yaml                  # manifest: description
├── Dockerfile.stack-root.tmpl  # root-scope install steps (system-wide
│                               # toolchains), rendered into the base image
└── Dockerfile.stack-user.tmpl  # user-scope install steps (per-user version
                                # managers like nvm/rustup) — ship one
                                # fragment or both
```

Fragments are Go templates with one variable, `{{.BuildKitEnabled}}`, for
emitting `--mount=type=cache` apt cache mounts only when BuildKit renders
the build. Every fragment self-guards: it skips its install when the image
already provides the tool, so declaring a stack that the base already
carries is always safe.

Planned additions (ongoing): a `pi` harness and the remaining embedded
components.
