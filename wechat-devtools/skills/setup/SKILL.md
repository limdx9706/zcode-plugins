---
name: setup
description: Check or install WeChat DevTools and its CLI, enable the service port, complete login, provide the CLI token when needed, and verify with one read-only call. Run before mini program work when the environment state is unknown.
---

# 设置微信开发者工具 CLI

只做本机依赖准备和只读验证。已有环境直接复用；不因为再次运行 setup 就重装 IDE、重新登录或改写用户配置。

## 0. 四层状态，逐层判断

**插件已安装 ≠ CLI 可用 ≠ 已登录 ≠ 有业务权限。** 按顺序确认，任何一层缺失就停在那一层并明确报告，不要把上层的成功当成本层通过。

## 1. IDE 与 CLI 是否安装

```bash
ls -d /Applications/wechatwebdevtools.app
command -v wechatidecli
```

- **两者都在** → 进第 2 步。
- **app 在、`wechatidecli` 不在** → CLI 随 IDE 提供，不必安装，直接用完整路径调用即可（不需要 sudo、不必建软链）：

  ```bash
  /Applications/wechatwebdevtools.app/Contents/MacOS/cli islogin --lang zh
  ```

  后续所有 `wechatidecli` 命令都可换成该完整路径。是否在 PATH 建软链由用户决定，不要擅自改动系统目录。
- **两者都不在** → 需要先安装 IDE。把官方下载页给用户，由用户自己完成 GUI 安装（不要在不可见环境里静默下载安装桌面应用）：

  https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html

  安装完成后回到本节重新检查，不要凭用户一句"装好了"就继续。

**版本获取**：`defaults read /Applications/wechatwebdevtools.app/Contents/Info.plist CFBundleShortVersionString`（本机实测 36.6.0）。

> 陷阱：`wechatidecli --version` 不是有效参数。它会忽略参数、打印根帮助并以**退出码 0** 结束，容易被误判为"版本已确认"。用上面的 plist 命令取版本。

## 2. 服务端口

CLI 通过 IDE 的本地 HTTP 服务通信。`islogin` 成功时 stderr 会打印：

```
✔ IDE 已启动，HTTP 服务地址 http://127.0.0.1:<port>
```

看到这一行说明服务端口已开启。若报连接失败 / 端口相关错误，引导用户在 IDE 里打开 **设置 → 安全设置**，开启 **服务端口**，并确认「CLI/HTTP 调用」可用。

- 需要固定端口时显式传 `--port <port>`；IDE 已用其它端口启动时必须先 `quit`，否则端口冲突。
- 端口是本地回环地址，不要把它当公网可达地址报告。

## 3. 登录

```bash
wechatidecli islogin --lang zh
```

- `{"login":true}`：已登录，进第 4 步。
- `{"login":false}`：需要用户参与。执行 `wechatidecli login`，IDE 会跳出扫码窗口，把控制权交还用户去扫码；不要在同一轮阻塞等待，也不要索取用户的微信凭证。
- 网络错误不等于未登录：报告具体错误，不要反复 `login`。

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

成功标准：退出码 0，且 stdout 最后一行 JSON 为 `{"login":true}`。这一步证明 CLI 与 IDE 通、账号已登录，**不证明**某个 appid 有上传或云开发权限——那要等具体业务命令返回才算。

## 完成报告

报告四项实际情况：IDE 与 CLI 是否就位（含版本、调用路径）、服务端口是否可用（端口号）、登录状态、令牌是否已配置。把缺失的步骤明确列出来（未装 IDE / 未开服务端口 / 未登录 / 缺令牌），并给出对应的下一步动作。不要把「插件已安装」当成「CLI 已可用」。
