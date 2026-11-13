# 微信开发者工具 CLI 插件

[English](./README.md)

本插件是微信开发者工具自带 CLI（命令 `wechatidecli` / `cli`）的轻量 Skill 封装：使用用户本机已安装的 IDE 与 CLI，不内置 CLI 二进制、凭证、MCP 或远程服务。

封装由 lidongxin 维护，版本 0.1.0；这不是腾讯官方发布的插件。上游 IDE、CLI、文档和商标仍归腾讯所有。

## 快速 setup

运行 `/wechat-devtools:setup`。先复用已安装的 IDE 与登录态，只在缺失时处理安装：`command -v wechatidecli` 确认 CLI，`wechatidecli islogin` 确认登录，stderr 里的 `HTTP 服务地址` 即为当前 IDE 端口。

CLI 通过 IDE 的本地 HTTP 服务通信，需在 IDE 的 **设置 → 安全设置** 开启服务端口。未登录时由 `wechatidecli login` 拉起扫码窗口，交还用户完成。令牌（`WECHAT_DEVTOOLS_CLI_TOKEN`）只在报鉴权错误时需要，不由 Agent 索取或回显。登录成功不等于某 appid 具备上传权限，两者分开报告。

## Skills

| Skill | 入口 | 用途 |
| --- | --- | --- |
| `setup` | `/wechat-devtools:setup` | 检查 IDE 与 CLI、开启服务端口、确认登录、跑一次只读验证 |
| `cli` | 自动按上下文触发 | 打开/关闭项目、预览二维码、上传版本、构建 npm、云函数、自动化 |

## 安全边界

- 标准输出是 JSON、进度条在 stderr；只解析 stdout 最后一个非空行，不以 stderr 有输出判失败。
- `upload`（上传正式版本）、`cloud functions deploy`、`build-ipa/apk`、`quit` 会先展示项目、appid、版本号和影响面，并请求确认。
- 上传前校验 `project.config.json` 中的 appid 与用户所指项目一致，不就近猜项目。
- 不索取或回显 CLI 令牌、登录二维码之外的凭证，也不读取用户配置文件内容。
- 本插件只驱动本机 IDE；CI/无头发布走 `miniprogram-ci`（上传密钥 + IP 白名单），属于另一套机制。

## 上游文档

- [微信开发者工具 CLI](https://developers.weixin.qq.com/miniprogram/dev/devtools/cli.html)
- [miniprogram-ci](https://developers.weixin.qq.com/miniprogram/dev/devtools/ci.html)

启用或更新插件后请新建 ZCode session，确保 Skill 清单刷新。
