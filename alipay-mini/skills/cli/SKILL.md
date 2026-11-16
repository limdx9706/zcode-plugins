---
name: cli
description: Use the Alipay mini program CLI (minidev) for Alipay-ecosystem mini programs — build without the IDE, run the dev server, preview on device, remote debug, upload, submit for review, and manage versions. Use when the task involves a 支付宝小程序 or any minidev-driven mini program project.
---

# 使用支付宝小程序 CLI

所有支付宝小程序操作通过本机 `minidev` 完成。若本回合没有可信的 setup 结果，先读并执行 `../setup/SKILL.md` 的检查。

## 调用契约（先记住这三条）

1. **加 `--machine-output` 后，stdout 只有一个 JSON 信封**：`{"success":true}` 或 `{"success":false,"message":...,"stack":...}`。stderr 通常为空，失败信息在 JSON 里而不是退出码里——但这个 CLI **失败时退出码是 255**（实测），所以两样都要看：退出码非 0 或 `success != true` 都算失败。
2. **`--help` 在三级子命令上不可用**：`minidev app list --help` 会报 `unknown command 'app list'`，换成 `-h` 就正常。二级（`minidev app --help`）和一级都正常，只是规律不统一。
3. **`build` 的 `--machine-output` 输出极大**：返回体里 `codeInjections` 塞了整个 webpack runtime。只取 `project`、`dist`、`result`（`size` / `tarFilePath` / `outFilePath` / `components`），不要把整段贴回对话。

## 与微信工具链的根本差异

`minidev` 是**独立 CLI**：自带编译器（`~/.minidev/compilers/cubebuild@<ver>/`），构建、预览、上传都不需要 IDE 运行，也不需要额外的 CI 包。所以：

- 不存在"服务端口"、"拉起 IDE"、"端口冲突"这类问题
- `build` / `pack` / `dev` 属于纯本地操作，不需要授权
- 需要授权的只有碰到平台接口的命令：`preview`、`remote-debug`、`upload`、`audit`、`app *`

## 确认授权与 appId

```bash
minidev app list --machine-output
```

返回数组即已授权，同时给出可用的 `appId` / `appName`。这是本 CLI 最划算的一次探测：一次调用同时验证授权、列举应用。

- 报授权错误 → 进入 setup 的授权步骤，不要反复重试业务命令
- 拿到多个 app 而用户没说清是哪个 → 问，不要按名称相似猜

## 命令速查

| 目的 | 命令 | 关键参数 | 性质 |
|---|---|---|---|
| 编译 | `build` | `-p` 项目、`-o` 产物目录、`--typescript` `--less` `--minify` `--source-map` | 本地 |
| 开发服务器 | `dev` | `--hmr` `--host` `--port` | 本地，常驻 |
| 打包源码 | `pack` | `--dir` `--filename` | 本地 |
| 真机预览 | `preview` | `-a` 必填、`--no-auto-push`、`--page` `--query` `--scene` | 平台 |
| 真机调试 | `remote-debug` | `-a` 必填、`--auto-open-devtools` | 平台 |
| 上传版本 | `upload` | `-a` 必填、`-v` 版本、`--version-description`、`-e` 设体验版 | **发布链路** |
| 提审 | `audit` | `-a`、`-v`、`--version-description`、`--version-screenshot`、`--auto-publish` | **最高风险** |
| 取消提审 | `cancel-audit` | `-a`、`-v` | 破坏性 |
| 应用与版本 | `app list` / `app get-uploaded-version` / `app get-uploaded-version-list` / `app set-experience` / `app cancel-experience` / `app delete-version` | 见 `-h` | 混合 |
| 启动 IDE | `ide` | `--app-path`、`start-sim` | 本地 |
| 新建项目 | `create` | `--name` `--stack native\|typescript` `--type mini-program\|plugin` `--yes` | 本地 |
| 授权 | `login` | `-c` 端类型 | 需要用户参与 |
| 资源/缓存 | `download-assets` / `clean-build-cache` / `setup-build` | `--with-compiler` `--clean-previous` | 本地 |
| 配置 | `config set\|get <configName>` | — | 本地 |

端类型 `-c` 默认 `alipay`；`preview` / `upload` 目标不是支付宝 App 时必须显式指定（`ampe` / `amap` / `quark` 等）。

## 项目定位

- `-p/--project` 省略时取当前工作目录；`preview` / `remote-debug` / `upload` / `audit` 的 `--app-id` 是**必填**，不要从目录名推断。
- 校验 `mini.project.json`（或 `app.json`）里的 appid 与用户所指应用一致，不一致先问。
- **项目路径不能经过软链**：在 `/tmp`（macOS 上是 `/private/tmp` 的软链）下 `build` 会报 `Module not found: Error: outside of the project`，换个真实路径立刻成功。用 `realpath <项目>` 确认。
- `create --dir` 目前是坏的：help 说它是父级目录，实现里当 boolean 用，传值直接抛 `The "path" argument must be of type string`。正确姿势是先 `cd` 到目标父目录再 `minidev create`。

## 写操作边界

以下必须先把目标、appid、版本号与影响面摊开，并取得用户确认：

- **`audit`（提审）**：对外提交审核。`--auto-publish` 会在通过后**自动上架**，属于不可逆的对外动作，必须用户明确要求才加。截图是必填项（最多 4 张、单张 4MB 内、最大 2160×3840），缺截图就停下问用户要。**权限门槛**：官方说明提审与 `--auto-publish` 仅**应用管理员及以上角色**的开发工具密钥生效——普通开发者密钥能上传但提不了审，报权限不足时按这个方向解释，不要让用户反复重试。
- **`upload`**：上传正式版本。`-e/--experience` 会顺手把该版本设为体验版，等于对外可见，一并确认。
- **`app delete-version`** / **`cancel-experience`** / **`cancel-audit`**：删除或撤销线上状态，确认后再动。
- **`ide`**：会拉起 GUI 窗口抢焦点，用户正在用电脑时先说明。

只读或本地可回滚的操作直接做：`build`、`pack`、`dev`、`app list`、`app get-uploaded-version*`、`config get`、`clean-build-cache`。

## 预览二维码

官方行为：`preview` 先构建，再推送到开放平台生成预览版，然后**在终端显示预览二维码**，同时打印两个可用的东西：

```
✔ 生成预览码成功
二维码图片地址 <url>
Schema Url <...>
[ 终端 ASCII 二维码 ]
```

所以交给用户的最优解是**把「二维码图片地址」那个 URL 给出来**（或 Read 下来当图展示），并说明这是预览版、需要开发者或体验成员身份、有效期有限。Schema Url 适合让用户直接唤起支付宝打开。

- `--auto-push` 默认开启：若目标端支持，会**额外**把小程序推到手机，免扫码。它不影响二维码是否输出，别为了"拿到二维码"去关它。
- 目标端不是支付宝 App 时必须显式指定 `-c`（`ampe` / `amap` / `quark` 等），否则二维码给错端。
- 带页面与参数预览：`--page <pages/x/x>`、`--page-query`（onLoad 可取）、`--query`（app.js onLaunch 可取）、`--scene`（channelId）。
- 用户在 `minidev dev` 里可以直接输入 `preview` / `remote-debug` / `web` / `ide`，走 DevServer 产物、比完整构建快，但官方说明这类模式**仅支持整包**，无法验证分包。

预览失败（授权缺失、appId 无权限、编译错误、域名白名单）时贴原始 `message`，不要换参数碰运气。

## 凭证与隐私

- 凭据是**开发工具密钥**（官方名称；CLI 帮助里叫"身份密钥"），两种来源：`minidev login` 快速授权后自动生成在 `~/.minidev/config.json`，或在开放平台 **账户中心 → 密钥管理 → 开发工具密钥 → 生成身份密钥** 下载后用 `--identity-key-path` 指定。
- 该文件里含 `privateKey`，**不要 cat/读取/回显**，只传路径。
- **同一支付宝用户同一时刻只有一份开发工具密钥有效**：在另一台机器 `minidev login` 或在网页重新生成，都会让旧的失效。CI/多机场景用分发 `config.json` 或 `MINIDEV_RUNTIME_CONFIG` 环境变量注入，不要每台都 login。
- 上传后的版本号、体验版状态、审核单号可以报告；appId 之外的账号信息按需最小化。

## 完成报告

说明实际执行的命令、项目路径、appid、端类型，以及读取或写入的结果。发布链路（`upload` / `audit`）未获确认时只给待执行命令，不要代为执行。失败时区分"本地编译失败"和"平台接口失败"，前者贴编译错误，后者贴 `message` 与缺失的权限。

## 来源

- [快速开始](https://opendocs.alipay.com/isv/02q17j.md)
- [指令列表概览](https://opendocs.alipay.com/isv/02q29z.md)
- [preview 真机预览](https://opendocs.alipay.com/mini/02q3al)
- [upload 上传项目](https://opendocs.alipay.com/mini/02q3an)
