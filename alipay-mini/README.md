# Alipay Mini Program CLI Plugin

[中文文档](./README_CN.md)

This plugin is a lightweight Skill wrapper around the official Alipay mini program CLI (`minidev`). It uses the user's locally installed CLI and does not bundle a CLI binary, credentials, an MCP server, or a remote service.

Wrapper maintained by lidongxin, version 0.1.0; this is not an Ant Group-published plugin. Upstream CLI software, documentation, and trademarks remain attributed to their respective owners.

## Quick setup

Run `/alipay-mini:setup`. Reuse an installed CLI and existing authorization; only handle what is missing:

```bash
node -v && npm -v                         # official requirement: Node 16+
npm i -g minidev --registry=https://registry.npmmirror.com
minidev download-assets --with-compiler   # only when the compiler is missing (~356MB cache in ~/.minidev)
minidev app list --machine-output         # probes authorization and lists usable appIds
```

When unauthorized, pick one: `minidev login` (a QR code appears in the terminal; scan it with the Alipay app — the command polls, so let the user run it in their own terminal), or download an identity key from the open platform and pass `--identity-key-path`. Only the key path is passed around, never its contents. A successful authorization does not prove upload or review permission for a specific app; report the two separately.

## Skills

| Skill | Entry | Purpose |
| --- | --- | --- |
| `setup` | `/alipay-mini:setup` | Check/install Node and minidev, fetch build assets, complete authorization, run one read-only verification |
| `cli` | Context-triggered | Build, dev server, device preview/debug, upload, submit for review, version management |

## Difference from the WeChat toolchain

`minidev` is a standalone CLI with its own bundled compiler: it does **not** need the IDE running, and there is no service port or port conflict. `build` / `pack` / `dev` are local and need no authorization; only `preview` / `remote-debug` / `upload` / `audit` / `app *` touch platform APIs.

## Safety boundary

- Publishing actions require a target and account summary first: `upload` (`-e` also sets the experience version), `audit` (review submission; `--auto-publish` publishes automatically on approval), `app delete-version`, `cancel-audit`, `cancel-experience`.
- Before building, verify the appid in `mini.project.json` / `app.json` matches the app the user means; never guess by similar names.
- Project paths must not traverse symlinks: under `/tmp` the build fails with `outside of the project`; confirm with `realpath`.
- Never request or echo identity keys or authorization tokens, and never read user configuration files.
- `build --machine-output` embeds the full webpack runtime; read only `project` / `dist` / `result`.

## Upstream documentation

- [Mini program CLI overview](https://opendocs.alipay.com/isv/02q17h)
- [preview](https://opendocs.alipay.com/mini/02q3al)
- [upload](https://opendocs.alipay.com/mini/02q3an)
- [npm: minidev](https://www.npmjs.com/package/minidev)

Open a new ZCode session after enabling or updating the plugin so the Skill catalog is refreshed.
