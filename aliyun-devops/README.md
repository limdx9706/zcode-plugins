# Yunxiao (云效) CLI Plugin

[中文文档](./README_CN.md)

This plugin is a lightweight Skill wrapper around the Yunxiao CLI (`aliyun devops`). Yunxiao has no standalone CLI binary: it is the product plugin `aliyun-cli-devops` of the Alibaba Cloud CLI, reachable only as `aliyun devops`. The plugin uses the user's locally installed CLI and does not bundle a CLI binary, credentials, an MCP server, or a remote service.

Wrapper maintained by lidongxin, version 0.1.0; this is not an Alibaba Cloud-published plugin. Upstream CLI software, documentation, and trademarks remain attributed to Alibaba Cloud.

## Quick setup

Run `/aliyun-devops:setup`. Reuse the installed plugin and token first: `aliyun devops version` for the plugin, `aliyun devops base-get-user-by-token` for identity. Install only when missing with `aliyun plugin install --names aliyun-cli-devops`.

Yunxiao authenticates with a **personal access token** (`pt-` prefix), not an Alibaba Cloud AccessKey; `aliyun devops` does not read `~/.aliyun/config.json`. Keep the token in `~/.yunxiao_env` (mode 600) and load it from `~/.zshrc` with `[ -f ~/.yunxiao_env ] && source ~/.yunxiao_env`. The organization comes from `ALIBABA_CLOUD_YUNXIAO_ORGANIZATION_ID` or `--organization-id`; when absent no error is raised and the token's default organization is used.

## Skills

| Skill | Entry | Purpose |
| --- | --- | --- |
| `setup` | `/aliyun-devops:setup` | Check the plugin, configure token and organization, run one read-only verification |
| `cli` | Context-triggered | Pipeline runs and logs, deploy change orders, Codeup repositories and MRs, work items and effort hours, packages, tests |

## Coverage

Enumerated from `aliyun devops --help`: 441 subcommands, all organized under `skills/cli/references/`.

| Module | Prefix | Count |
| --- | --- | --- |
| Flow pipelines | `flow-` | 86 |
| Codeup code management | `codeup-` | 115 |
| Projex project collaboration | `projex-` | 76 |
| AppStack application delivery | `app-stack-` | 93 |
| Base organization and members | `base-` | 37 |
| Testhub | `test-hub-` | 26 |
| Packages artifact registry | `packages-` | 6 |
| Insight / version | `insight-` `version` | 2 |

Each reference lists the command, its purpose, and **required** parameters; full parameters come from `aliyun devops <command> --help`.

## Safety boundary

- Running pipelines, creating change orders, merging MRs, updating work items, logging effort hours, and deleting artifacts are remote writes: the organization, resource IDs, and a change summary are shown before execution and require confirmation.
- The token never goes into command lines, shell history, or conversation output; prefer `~/.yunxiao_env` with mode 600.
- When the organization is unclear, confirm via `base-list-organizations` instead of guessing from similar names.
- Response shapes vary (bare arrays, `result`, or named keys); parse defensively and keep only the needed fields.

## Upstream documentation

- [Yunxiao OpenAPI](https://help.aliyun.com/zh/yunxiao/developer-reference/)
- [Personal access token](https://help.aliyun.com/zh/yunxiao/developer-reference/obtain-personal-access-token)
- [Alibaba Cloud CLI](https://github.com/aliyun/aliyun-cli)

Open a new ZCode session after enabling or updating the plugin so the Skill catalog is refreshed.
