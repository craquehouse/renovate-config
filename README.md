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

### Why `local>` and not `github>`

`local>` resolves against whichever platform Renovate is pointed at, so the same
config works from either host. This repo is mirrored to
`git.craquehouse.cc/craquehouse/renovate-config`, and `default.json` refers to its
own siblings with `local>` for exactly that reason — a Renovate run on
git.craquehouse.cc resolves them on git.craquehouse.cc, and one on github.com
resolves them on github.com. Hardcoding `github>` would send every self-hosted run
back out to github.com for each sub-preset, and make preset resolution depend on a
GitHub token and rate limit.

Keep new sibling references in `default.json` as `local>`.

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
`local>craquehouse/renovate-config:groups.json5`.
