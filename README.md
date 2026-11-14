# zcode-plugins

Personal ZCode plugin marketplace.

## Add as a marketplace

**Plugin Marketplace → Add → Add Plugin Marketplace**, then paste:

```
limdx9706/zcode-plugins
```

Pin a branch or tag when needed: `limdx9706/zcode-plugins@main`.

## Plugins

| Plugin | What it does | Requirements |
|---|---|---|
| [`wechat-devtools`](./wechat-devtools) | Drives the local WeChat DevTools CLI — open/close projects, preview QR codes, upload versions, build npm, deploy cloud functions, run automation | WeChat DevTools installed at `/Applications/wechatwebdevtools.app`, IDE logged in, service port enabled |
| [`alipay-mini`](./alipay-mini) | Drives the Alipay mini program CLI (`minidev`) — build without the IDE, dev server, device preview/debug, upload, submit for review, version management | Node/npm, `minidev` installed globally, machine authorized via `minidev login` or an identity key |
| [`aliyun-devops`](./aliyun-devops) | Drives the Yunxiao (云效) CLI — Flow pipelines and logs, AppStack deploys, Codeup repos and MRs, Projex work items and effort hours, packages, tests. Covers all 441 `aliyun devops` subcommands | Alibaba Cloud CLI with the `aliyun-cli-devops` plugin, Yunxiao personal access token (`~/.yunxiao_env`) |
| [`wiztree`](./wiztree) | Drives the local WizTree disk analyzer — scan drives/folders, export CSV and treemap PNG, locate large files and duplicates, plan safe cleanup | Windows, WizTree 4.x (winget or portable), Administrator rights recommended for full scans |

## Layout

```
marketplace.json                 # market manifest, read from the repo root
<plugin-name>/
  .zcode-plugin/plugin.json      # plugin manifest; directory name must match `name`
  skills/<skill-name>/SKILL.md   # skill: YAML frontmatter with name + description
```

Plugin entries use a relative `source` resolved against the repo root.

## License

MIT
