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
| `helmPostUpgradeTasks.json5` | Post-upgrade tasks for Helm charts — **opt-in**, see below                 |
| `labels.json5`               | PR/issue labels                                                            |
| `semanticCommits.json5`      | Semantic commit conventions                                                |

Individual presets can also be extended directly, e.g.
`local>craquehouse/renovate-config:groups.json5`.

### `helmPostUpgradeTasks.json5` is opt-in

It is deliberately **not** in `default.json`, because it depends on the
execution environment rather than on the repo alone. It runs `helm-docs` and
`helm-schema` as post-upgrade tasks, so it needs a Renovate runtime that both
installs those binaries and allowlists the commands.

The org-wide workflow in `craquehouse/.github` did exactly that — a
`docker-cmd-file` entrypoint fetching both tools plus
`RENOVATE_ALLOWED_COMMANDS: '["^helm-docs ", "^helm-schema "]'` — and the preset
worked there. The self-hosted `renovate-operator` path replicates neither, so
once repos moved to it every PR failed the `renovate/artifacts` check. Because
`default.json` also sets `:automergeBranch`, that red check silently blocked
automerge while CI itself stayed green, which made it easy to miss.

Inheriting an environment-dependent preset by default meant every consumer paid
that cost; none currently benefits, since no craquehouse repo ships charts under
`charts/` or `deploy/helm/`.

A chart repo should extend it alongside the default:

```json5
{
    extends: [
        "local>craquehouse/renovate-config",
        "local>craquehouse/renovate-config:helmPostUpgradeTasks.json5",
    ],
}
```

That repo's Renovate runtime must also provide `helm-docs` and `helm-schema` and
allowlist both commands — see `.github/renovate-entrypoint.sh` and
`RENOVATE_ALLOWED_COMMANDS` in `craquehouse/.github` for a working reference.
`allowedCommands` is a self-hosted administrator setting; a repo preset cannot
grant it.
