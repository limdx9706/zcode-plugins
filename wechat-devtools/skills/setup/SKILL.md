---
name: setup
description: Check or install WeChat DevTools and its CLI, enable the service port, complete login by handing the QR code to the user, provide the CLI token when needed, and verify with one read-only call.
---

# 设置微信开发者工具 CLI

只做本机依赖准备和只读验证。已有环境直接复用；不因为再次运行 setup 就重装 IDE、重新登录或改写用户配置。

## 0. 四层状态，逐层判断

**插件已安装 ≠ CLI 可用 ≠ 已登录 ≠ 有业务权限。** 按顺序确认，缺哪层就停在哪层并明确报告，不要把上层的成功当成本层通过。

## 1. IDE 与 CLI 是否安装

```bash
ls -d /Applications/wechatwebdevtools.app
command -v wechatidecli
```

- **两者都在** → 进第 2 步。
- **app 在、`wechatidecli` 不在** → CLI 随 IDE 提供，不必安装，直接用完整路径调用（不需要 sudo、不必建软链）。官方文档给的 CLI 位置就是：

  ```bash
  # macOS: <安装路径>/Contents/MacOS/cli    Windows: <安装路径>/cli.bat
  /Applications/wechatwebdevtools.app/Contents/MacOS/cli islogin --lang zh
  ```

  后续所有 `wechatidecli` 命令都可换成该完整路径。是否在 PATH 建软链由用户决定，不要擅自改动系统目录。
- **两者都不在** → 需要先安装 IDE。把官方下载页给用户，由用户自己完成 GUI 安装（不要在不可见环境里静默下载安装桌面应用）：

  https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html

  安装完成后回到本节重新检查，不要凭用户一句"装好了"就继续。

**版本获取**：`defaults read /Applications/wechatwebdevtools.app/Contents/Info.plist CFBundleShortVersionString`（本机实测 36.6.0）。

> 陷阱：`wechatidecli --version` 不是有效参数。它会忽略参数、打印根帮助并以**退出码 0** 结束，容易被误判为"版本已确认"。用上面的 plist 命令取版本。

## 2. 服务端口（CLI 的前提）

官方文档明确：**使用命令行前必须先在开发者工具的 设置 → 安全设置 中开启服务端口。** 没有这一步，所有 CLI 和 HTTP 调用都不可用。

`islogin` 成功时 stderr 会打印：

```
✔ IDE 已启动，HTTP 服务地址 http://127.0.0.1:<port>
```

看到这一行说明服务端口已开启。若报连接失败 / 端口相关错误，引导用户打开 **设置 → 安全设置** 开启 **服务端口**。

- 需要固定端口时显式传 `--port <port>`；官方说明：IDE 已用其它端口启动时必须先 `quit`，否则端口冲突。
- 端口是本地回环地址，不要把它当公网可达地址报告。

## 3. 登录

先探测：

```bash
wechatidecli islogin --lang zh
```

`{"login":true}` → 已登录，进第 4 步。

`{"login":false}` → 需要扫码。CLI 支持把二维码直接输出成图片，**Agent 应该走这条**，让用户在当前对话里就能扫：

```bash
wechatidecli login --qr-format image --qr-output login-qr.png --lang zh
```

生成后用 Read 把 png 展示给用户，并说明二维码有时效、过期就重跑。登录会阻塞等待扫码，所以：

- 不要在同一轮里无限等待；把二维码交出去后结束本轮，用户确认后再回第 3 步复核。
- 用户更愿意自己在终端操作时，给他 `wechatidecli login`（终端里直接画二维码，`-f terminal` 为默认）。
- 需要把登录结果落盘时用 `--result-output <path>`。
- 不要索取用户的微信账号密码，也不要代扫。

## 4. 令牌（仅在报权限/鉴权错误时处理）

CLI 令牌来自 IDE 的 **设置 → 安全设置**，通过两种方式之一传入：

```bash
export WECHAT_DEVTOOLS_CLI_TOKEN=<令牌>   # 推荐：由用户在自己的终端设置
wechatidecli <command> --token <令牌>      # 临时传入
```

- 令牌值不要出现在对话、日志、插件文件或仓库里；需要长期使用就让用户写进自己的 shell profile。
- 不要在未报鉴权错误时主动索取令牌——本机大多数情况靠登录态就够。

## 5. 只读验证

```bash
wechatidecli islogin --lang zh
```

成功标准：**退出码 0** 且 stdout 最后一行 JSON 为 `{"login":true}`。官方约定返回码 `0` 为正常、`-1` 为错误，两样都要看。

这一步证明 CLI 与 IDE 通、账号已登录，**不证明**某个 appid 有上传或云开发权限——那要等具体业务命令返回才算。

## 完成报告

报告四项实际情况：IDE 与 CLI 是否就位（含版本、调用路径）、服务端口是否可用（端口号）、登录状态、令牌是否已配置。把缺失的步骤明确列出来（未装 IDE / 未开服务端口 / 未登录 / 缺令牌），并给出对应的下一步动作。不要把「插件已安装」当成「CLI 已可用」。
