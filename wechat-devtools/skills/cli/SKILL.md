---
name: cli
description: Use the local WeChat DevTools CLI (wechatidecli) for mini program work — open or close projects, generate preview QR codes, upload a version, build npm, deploy cloud functions, and drive automation. Use when the task involves a WeChat mini program or 微信开发者工具.
---

# 使用微信开发者工具 CLI

所有小程序 IDE 操作通过本机 `wechatidecli` 完成。若本回合没有可信的 setup 结果，先读并执行 `../setup/SKILL.md` 的检查。

## 调用契约（先记住这三条）

1. **标准输出是 JSON，进度条走 stderr。** 只解析 stdout 中最后一个非空行，不要把 stderr 的 `✔ / -` 提示当数据，也不要因为 stderr 有输出就判定失败。
2. **成功 = 退出码 0 且 JSON 语义成功。** 例如 `islogin` 返回 `{"login":true}`；`login:false` 是未登录而不是命令失败。
3. **命令会自动拉起 IDE。** 传 `--port` 时若无运行中的 IDE，CLI 会启动 IDE 窗口（前台弹出）。IDE 已用另一个端口启动时，必须先 `quit` 再换端口，否则报端口冲突。
4. **退出码是官方契约**：文档写明返回码 `0` 为正常、`-1` 为错误。结合 JSON 内容一起判断，不要只看其中一项。

## 范围

本 skill 只管**本机 IDE**。项目的 CI 发布路径（`miniprogram-ci`：上传密钥 + IP 白名单、无头运行、编译设置显式声明）是另一套机制，不在本 skill 内。用户提到 CI/流水线/无头发布时，不要拿 IDE CLI 冒充 CI，说明二者差异后让用户选。

## 确认身份与上下文

```bash
wechatidecli islogin --lang zh
```

输出示例（stdout）：`{"login":true}`，stderr 同时报告 `IDE 已启动，HTTP 服务地址 http://127.0.0.1:<port>`——这个地址就是当前 IDE 的 HTTP 端口，需要显式传 `--port` 时用它。

`{"login":false}` 时进入 setup 的登录步骤，不要反复重试业务命令。

## 命令选择

```bash
wechatidecli --help
wechatidecli <command> --help
```

注意：二级子命令的帮助不可用（`wechatidecli agent tool --help` 会打印根帮助）。到 `wechatidecli agent --help` / `wechatidecli cloud --help` 这一层为止，再往下按子命令名直接调。要中文帮助加 `--lang zh`，官方写法是 `cli --lang zh -h`。

## 命令速查

| 目的 | 命令 | 性质 |
|---|---|---|
| 查询登录态 | `islogin` | 只读 |
| 打开项目 / 新开窗口 | `open --project <dir>` / `open-other` | 本地 |
| 关闭项目 / 退出 IDE | `close` / `quit` | 影响用户现场 |
| 生成预览二维码 | `preview --qr-format image --qr-output <png>` | 只读（云端编译） |
| 持续预览 | `auto-preview` | 只读 |
| 上传版本 | `upload --version <v> --desc <d>` | **对外发布** |
| 构建 npm | `build-npm` | 本地构建 |
| 云函数 | `cloud functions list/info/deploy/inc-deploy/download` | deploy 是**远端写** |
| 云环境 | `cloud env` | 视子命令 |
| 自动化 | `auto` / `auto-replay` | 启动自动化端口 |
| Agent 自动化 | `agent start` / `agent chat` / `agent tool` / `agent get-dom` / `agent get-card-image` | 本地 |
| 编译引擎 | `engine build <projectpath>` | 本地构建 |
| 包产物 | `build-ipa` / `build-apk` | 本地构建 |
| 清缓存 | `cache` / `reset-fileutils` | 影响 IDE 状态 |

## 项目定位

- 优先 `--project <绝对路径>`（读该目录的 `project.config.json` 拿 appid）；只有没有本地项目目录时才用 `--appid`。两者同时给时 `--appid` 被忽略。
- 第三方平台代开发才用 `--ext-appid`。
- 路径不存在、目录里没有 `project.config.json`、或 appid 与用户所说不是同一个时，先问，不要就近猜一个项目。

## 写操作边界

以下操作必须先展示目标与摘要并取得确认：

- `upload`：展示项目路径、appid、版本号、描述。这是上传到微信后台的正式版本，属于对外发布链路。版本号/描述必须来自用户，不要自拟然后上传。
- `cloud functions deploy` / `inc-deploy`：展示环境、函数名、要部署的目录。
- `build-ipa` / `build-apk`：展示项目与输出设置。
- `quit` / `close`：会中断用户正在用的 IDE 现场；确认没有未保存的改动再执行。
- `cache` / `reset-fileutils`：会改动 IDE 本地状态。

只读或本地可回滚的操作（`islogin`、`preview`、`cloud functions list`、`agent get-dom`、`build-npm`、`engine build`）直接执行。

## 预览二维码

Agent 无法扫码，必须把二维码变成用户能看的东西。官方支持三种格式（`--qr-format`：`terminal` 默认 / `image` / `base64`），直接产出图片最省事：

```bash
wechatidecli preview --project <dir> --qr-format image --qr-output preview-qr.png --lang zh
```

- 生成后用 Read 把 png 展示给用户，并说明这是体验版预览码、有效期有限、需要开发者或体验成员身份才能打开。
- 需要塞进别的系统时用 base64：`--qr-format base64 --qr-output base64@<path>`（官方示例就是这个组合）。
- 想看包大小/分包信息加 `--info-output <path>`，输出是 JSON。
- 指定自定义编译条件（进哪个页面、带什么参数）用：

  ```bash
  wechatidecli preview --project <dir> --compile-condition '{"pathName":"pages/index/index","query":"x=1&y=2"}'
  ```

  **注意**：本机 36.6.0 的 `preview -h` 没有列出 `--compile-condition`，`auto-preview -h` 也没列 `--auto-preview-type mobile|pc`，但官方文档（2026-11-11 更新）有这两个参数，实测解析器也接受（不会报 unknown option）。属于"帮助未列出但可用"，不要因为 `-h` 没有就判定不支持。

- `auto-preview` 是"直接推到手机上"的变体（`--auto-preview-type mobile|pc`，见上面的帮助说明），要求已登录；用户要的是能扫的码就用 `preview`，别混用。

预览失败（编译错误、未登录、appid 无权限）时把原始错误贴出来，不要换命令碰运气。

## 凭证与隐私

- CLI 令牌通过 `--token` 或环境变量 `WECHAT_DEVTOOLS_CLI_TOKEN` 传入；不要在对话、日志、文件或命令回显里打印令牌值。
- 上传后的版本号、体验版链接、云环境 ID 可以报告；登录二维码、token、用户私有 appid 之外的信息按需最小化输出。

## 完成报告

说明实际执行的命令、项目路径与 appid、读取或写入的结果。远端写操作未获确认时只给出待执行命令，不要代为执行。失败时报告原始错误（端口冲突、未登录、编译失败、权限不足）并给出对应的下一步，不自行切换项目或重试写操作。
