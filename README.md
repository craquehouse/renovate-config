# renovate-config

Shared [Renovate](https://docs.renovatebot.com) presets for
[craquehouse](https://github.com/craquehouse) repositories.

## Usage

In a repo's `.renovaterc.json5` (or `renovate.json`), extend the default preset:

```json5
{
    $schema: "https://docs.renovatebot.com/renovate-schema.json",
    extends: ["local>craquehouse/renovate-config"],
}
```

`local>` resolves to `default.json`, which pulls in the modular presets below.

## Presets

| Preset                       | Purpose                                                                    |
| ---------------------------- | -------------------------------------------------------------------------- |
| `default.json`               | Entrypoint — composes the presets below plus upstream `config:recommended` |
| `annotated.json5`            | Regex/annotation-based custom managers                                     |
| `autoMerge.json5`            | Auto-merge rules                                                           |
| `groups.json5`               | Dependency grouping                                                        |
| `helmPostUpgradeTasks.json5` | Post-upgrade tasks for Helm charts                                         |
| `labels.json5`               | PR/issue labels                                                            |
| `semanticCommits.json5`      | Semantic commit conventions                                                |

Individual presets can also be extended directly, e.g.
`github>craquehouse/renovate-config:groups.json5`.
