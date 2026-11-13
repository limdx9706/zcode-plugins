# WeChat DevTools CLI Plugin

[中文文档](./README_CN.md)

This plugin is a lightweight Skill wrapper around the CLI bundled with WeChat DevTools (`wechatidecli` / `cli`). It uses the user's locally installed IDE and CLI and does not bundle a CLI binary, credentials, an MCP server, or a remote service.

Wrapper maintained by lidongxin, version 0.1.0; this is not a Tencent-published plugin. Upstream IDE, CLI software, documentation, and trademarks remain attributed to their respective owners.

## Quick setup

Run `/wechat-devtools:setup`. Reuse an installed IDE and existing login first; only handle installation when it is missing: verify the CLI with `command -v wechatidecli` (official location `Contents/MacOS/cli`, bundled with the IDE — nothing to install separately), then confirm login with `wechatidecli islogin` — the `HTTP 服务地址` line on stderr is the current IDE port.

The CLI talks to the IDE over a local HTTP service, and the official docs require enabling the service port under **Settings → Security Settings** in the IDE first. When not logged in, export the QR code as an image with `wechatidecli login --qr-format image --qr-output qr.png` and hand it to the user (`terminal` and `base64` are the other formats). The CLI token (`WECHAT_DEVTOOLS_CLI_TOKEN`) is only needed after an authentication error and is never requested or echoed by the agent. A successful login does not prove upload permission for a given appid; report the two separately.

## Skills

| Skill | Entry | Purpose |
| --- | --- | --- |
| `setup` | `/wechat-devtools:setup` | Check the IDE and CLI, enable the service port, confirm login, run one read-only verification |
| `cli` | Context-triggered | Open/close projects, preview QR codes, upload versions, build npm, deploy cloud functions, run automation |

## Safety boundary

- stdout carries JSON while progress spinners go to stderr; parse only the last non-empty stdout line and never treat stderr output as failure.
- `upload` (publishing a version), `cloud functions deploy`, `build-ipa/apk`, and `quit` require showing the project, appid, version, and blast radius plus user confirmation.
- Before uploading, verify that the appid in `project.config.json` matches the project the user means; never pick a nearby project.
- Never request or echo the CLI token or credentials beyond the login QR code, and never read the contents of user configuration files.
- This plugin only drives the local IDE. CI / headless publishing belongs to `miniprogram-ci` (upload key + IP allowlist) and is a separate mechanism.

## Upstream documentation

- [WeChat DevTools CLI](https://developers.weixin.qq.com/miniprogram/dev/devtools/cli.html)
- [miniprogram-ci](https://developers.weixin.qq.com/miniprogram/dev/devtools/ci.html)

Open a new ZCode session after enabling or updating the plugin so the Skill catalog is refreshed.
