---
name: setup
description: Check the local WeChat DevTools installation and CLI, enable the IDE service port, provide the CLI token, verify login, and run one read-only call before any mini program operation.
---

# 设置微信开发者工具 CLI

只做本机依赖准备和只读验证。已有环境直接复用；不因为再次运行 setup 就重装 IDE、重新登录或改写用户配置。

## 1. 检查安装与 CLI

```bash
ls -d /Applications/wechatwebdevtools.app
command -v wechatidecli
wechatidecli --version
```

- `wechatidecli` 是 `/Applications/wechatwebdevtools.app/Contents/MacOS/wechatidecli` 的软链。软链缺失但 app 存在时，直接用完整路径调用，不自己去 `/usr/local/bin` 建软链（需要 sudo，且属于用户环境改动）。
- 版本可从 `defaults read /Applications/wechatwebdevtools.app/Contents/Info.plist CFBundleShortVersionString` 读（本机实测 36.6.0）。
- 未安装时安装包需要用户自己去微信官方下载；给出官方地址，不要在不可见的环境里静默下载安装 GUI 应用。

## 2. 服务端口

CLI 通过 IDE 的本地 HTTP 服务通信。`islogin` 成功时 stderr 会打印：

```
✔ IDE 已启动，HTTP 服务地址 http://127.0.0.1:<port>
```

看到这一行说明服务端口已开启。若报连接失败 / 端口相关错误，让用户在 IDE 里打开 **设置 → 安全设置**，开启 **服务端口**，并确认「CLI/HTTP 调用」可用。

- 需要固定端口时显式传 `--port <port>`；IDE 已用其它端口启动时必须先 `quit`，否则端口冲突。
- 端口是本地回环地址，不要把它当公网可达地址报告。

## 3. 登录

```bash
wechatidecli islogin --lang zh
```

- `{"login":true}`：已登录，进入第 4 步。
- `{"login":false}`：需要用户参与。执行 `wechatidecli login`，IDE 会跳出扫码窗口，把控制权交还用户去扫码；不要在同一轮阻塞等待，也不要索取用户的微信凭证。
- 网络错误不等于未登录：报告具体错误，不要反复 `login`。

## 4. 令牌（仅在报权限/鉴权错误时处理）

CLI 令牌来自 IDE 的 **设置 → 安全设置**，通过两种方式之一传入：

```bash
export WECHAT_DEVTOOLS_CLI_TOKEN=<令牌>   # 推荐：由用户在自己的终端设置
wechatidecli <command> --token <令牌>      # 临时传入
```

- 令牌值不要出现在对话、日志、插件文件或仓库里；需要长期使用就让用户写进自己的 shell profile。
- 不要在未报鉴权错误时主动索取令牌。

## 5. 只读验证

```bash
wechatidecli islogin --lang zh
```

成功标准：退出码 0，且 stdout 最后一行 JSON 为 `{"login":true}`。这一步只证明 CLI 与 IDE 通、账号已登录，**不证明**某个 appid 有上传/云开发权限——那要等具体业务命令返回才算。

## 完成报告

报告实际版本、CLI 路径、当前登录状态、探测到的 IDE HTTP 端口，以及仍缺失的步骤（未装、未登录、未开服务端口）。不要把「插件已安装」当成「CLI 已可用」，也不要凭用户一句"装好了"就跳过第 5 步。
