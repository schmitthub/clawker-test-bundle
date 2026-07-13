# clawker-test-bundle

UAT fixture for the clawker bundle install model. Ships an
[OpenCode](https://opencode.ai) harness:

| Component | Address | What it does |
|-----------|---------|--------------|
| `harnesses/opencode` | `schmitthub.test-bundle.opencode` | Installs the opencode CLI via the canonical standalone installer (version resolved from npm `opencode-ai`), persists `~/.config/opencode` + `~/.local/share/opencode` as volumes, stages the host's global `AGENTS.md`, and floors egress at `models.dev` + `api.anthropic.com` |

Declare and build it:

```yaml
bundles:
  - url: https://github.com/schmitthub/clawker-test-bundle.git
    ref: v0.2.2
```

```bash
clawker bundle install
clawker build -t schmitthub.test-bundle.opencode
```

Proof in a built image: `opencode` on PATH (`~/.local/bin/opencode`),
`CMD ["opencode"]`, and the two harness volumes mounted under the
container home.

UAT: unpinned default-branch tracking probe (2026-07-13).
