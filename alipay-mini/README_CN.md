# 支付宝小程序 CLI 插件

[English](./README.md)

本插件是支付宝小程序官方 CLI（命令 `minidev`）的轻量 Skill 封装：使用用户本机已安装的 CLI，不内置 CLI 二进制、凭证、MCP 或远程服务。

封装由 lidongxin 维护，版本 0.1.0；这不是蚂蚁集团官方发布的插件。上游 CLI、文档和商标仍归蚂蚁集团所有。

## 快速 setup

运行 `/alipay-mini:setup`。先复用已安装的 CLI 与授权，只在缺失时处理：

```bash
node -v && npm -v                         # 官方要求 Node 16 及以上
npm i -g minidev --registry=https://registry.npmmirror.com
minidev download-assets --with-compiler   # 编译器缺失时补下载（缓存约 356MB 在 ~/.minidev）
minidev app list --machine-output         # 探测授权，同时列出可用 appId
```

未授权时二选一：`minidev login`（终端出现授权二维码，用支付宝 APP 扫码；会轮询等待，所以让用户在自己的终端里执行），或从开放平台下载身份密钥后用 `--identity-key-path` 指定。密钥只传路径，不回显内容。授权成功不等于某应用具备上传/提审权限，两者分开报告。

## Skills

| Skill | 入口 | 用途 |
| --- | --- | --- |
| `setup` | `/alipay-mini:setup` | 检查/安装 Node 与 minidev、补编译资源、完成授权、只读验证 |
| `cli` | 自动按上下文触发 | 编译、DevServer、真机预览/调试、上传、提审、版本管理 |

## 与微信工具链的差异

`minidev` 是独立 CLI，自带编译器，**不需要 IDE 运行**，也没有服务端口或端口冲突问题。`build` / `pack` / `dev` 纯本地、无需授权；`preview` / `remote-debug` / `upload` / `audit` / `app *` 才碰平台接口。

## 安全边界

- 发布链路必须先确认目标与账号：`upload`（`-e` 会直接设体验版）、`audit`（提审，`--auto-publish` 通过后自动上架）、`app delete-version`、`cancel-audit`、`cancel-experience`。
- 编译前校验 `mini.project.json` / `app.json` 的 appid 与用户所指应用一致，不按名称相似猜。
- 项目路径不能经过软链：`/tmp` 下 `build` 会报 `outside of the project`，用 `realpath` 确认。
- 不索取或回显身份密钥、授权令牌，也不读取用户配置文件内容。
- `build --machine-output` 返回体包含完整 webpack runtime，只取 `project` / `dist` / `result`。

## 上游文档

- [小程序 CLI 产品介绍](https://opendocs.alipay.com/isv/02q17h)
- [preview 真机预览](https://opendocs.alipay.com/mini/02q3al)
- [upload 上传项目](https://opendocs.alipay.com/mini/02q3an)
- [npm: minidev](https://www.npmjs.com/package/minidev)

启用或更新插件后请新建 ZCode session，确保 Skill 清单刷新。
