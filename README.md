# clawker-test-bundle

UAT fixture for the clawker bundle install model. Ships two marker stacks:

| Component | Address | Proof in a built image |
|-----------|---------|------------------------|
| `stacks/hello` (root scope) | `ajschmitt.test-bundle.hello` | `/usr/local/share/clawker-uat/hello` + `hello-uat` on PATH |
| `stacks/greet` (user scope) | `ajschmitt.test-bundle.greet` | `~/.clawker-uat-greet` |

Declare it:

```yaml
bundles:
  - url: https://github.com/schmitthub/clawker-test-bundle.git
    ref: v0.1.0

build:
  stacks: [ajschmitt.test-bundle.hello, ajschmitt.test-bundle.greet]
```
