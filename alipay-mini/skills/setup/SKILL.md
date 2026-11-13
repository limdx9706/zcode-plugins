---
name: setup
description: Check or install Node.js and the Alipay mini program CLI (minidev), download build assets, authorize the machine via the terminal QR code, and verify with one read-only call.
---

# 设置支付宝小程序 CLI

目标是让 `minidev` 可用且已授权。已有环境直接复用；不因为再次运行 setup 就重装、升级或重新授权。

## 0. 四层状态，逐层判断

**插件已安装 ≠ CLI 可用 ≠ 已授权 ≠ 有业务权限。** 按 1 → 2 → 3 → 4 顺序确认，缺哪层就停在哪层并说明，不要把上层成功当成本层通过。

## 1. 安装 CLI

官方要求 **Node 16 及以上**。

```bash
node -v && npm -v          # 没有 Node 先装 Node（官方：https://nodejs.org）
command -v minidev && minidev -v
```

- 版本号能打印（本机实测 `2.2.5`，退出码 0）→ 进第 2 步。
- 没有 `minidev` → 用官方安装命令：

  ```bash
  npm i -g minidev --registry=https://registry.npmmirror.com
  ```

  官方提醒：安装过程会下载调试器、模拟器类资源，保持网络畅通。装完回到本节重查。
- 用户明确要求升级时才更新，官方写法：

  ```bash
  npm update -g minidev && minidev -v
  ```

- 全局安装位置：`$(npm prefix -g)/lib/node_modules/minidev`，可执行软链 `$(npm prefix -g)/bin/minidev`（本机为 `/usr/local/lib/node_modules/minidev` 与 `/usr/local/bin/minidev`）。
- 内网 npm 源装不上时报告该源不可达，不要偷偷换成公网源；官方命令里的 `--registry` 是给公网用户用的。

## 2. 编译资源

`minidev` 自带编译器，缓存在 `~/.minidev/`（本机实测 356 MB）。若 `build` 报编译器缺失（`spawn .../mini-pkg-builder ENOENT`），补下载：

```bash
minidev download-assets --with-compiler
```

需要强制重下时加 `--clean-previous`。磁盘紧张时先用 `du -sh ~/.minidev` 报告实际占用，再让用户决定。

## 3. 授权

先探测：

```bash
minidev app list --machine-output
```

返回非空数组 → 已授权，同时得到 `appId` / `appName`，进第 4 步。

报授权错误 → 官方授权方式是**扫码**：

```bash
minidev login
```

官方文档原文：执行后**终端界面会出现授权二维码**，在二维码过期前用支付宝 APP 扫码即可完成授权。实现上它会轮询等待，状态依次是「请使用支付宝扫描上面的二维码」→「已扫描, 请在手机上确认」→「正在完成授权」→「完成授权」。

**因此不要在 Agent 里阻塞调用 `minidev login`**——二维码是终端 ASCII 块，转述给你没用。正确做法是让用户在自己的终端里执行它，扫完回来再跑第 3 步探测。CLI 没有单独生成二维码图片的子命令，别去找 `minidev auth` 之类不存在的东西。

另一条路是身份密钥：用户从开放平台下载后，命令里显式指定路径。

```bash
minidev app list --machine-output --identity-key-path <路径>
```

身份密钥在开放平台的应用详情里下载，具体菜单以控制台当前版本为准；找不到就直接走扫码授权。密钥只传路径，内容不要出现在对话、文件、仓库或命令行历史里。

多端场景（`ampe` / `amap` / `quark` 等）才需要 `-c <clientType>`，默认 `alipay`，不要替用户改端类型。

## 4. 只读验证

```bash
minidev app list --machine-output
```

成功标准：退出码 0，且 stdout 是 JSON 数组，至少含用户要操作的那个 `appId`。

这一步只证明**电脑已授权、能看到该应用**，不证明它有 `upload` / `audit` 权限。上传或提审权限要等对应命令真正返回才算，两者分开报告。

## 完成报告

报告：Node/npm 版本、`minidev` 版本与可执行路径、`~/.minidev` 资源占用与编译器是否就位、授权方式（扫码还是密钥路径）、可见应用（appId + 名称，只列必要部分）。

缺失项写清是哪一层：没 Node / 没 CLI / 没编译资源 / 没授权 / 无业务权限，并给出下一步那条命令。

## 来源

- [快速开始（官方，含安装与授权）](https://opendocs.alipay.com/isv/02q17j.md)
- [小程序 CLI 产品介绍](https://opendocs.alipay.com/isv/02q17h.md)
