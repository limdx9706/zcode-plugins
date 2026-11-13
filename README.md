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
