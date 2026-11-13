---
name: setup
description: Check or install Node.js and the Alipay mini program CLI (minidev), download build assets, complete platform authorization, and verify with one read-only call. Use when minidev is missing, unauthorized, or its state is unknown.
---

# 设置支付宝小程序 CLI

目标是让 `minidev` 可用且已授权。已有环境直接复用；不因为再次运行 setup 就重装、升级或重新授权。

## 0. 四层状态，逐层判断

**插件已安装 ≠ CLI 可用 ≠ 已授权 ≠ 有业务权限。** 按 1 → 2 → 3 → 4 顺序确认，缺哪层就停在哪层并明确说缺什么，不要把上层成功当成本层通过。

## 1. CLI 是否安装

```bash
command -v minidev
minidev -v
```

- 第一条有路径、第二条打印版本号（本机实测 `2.2.5`，退出码 0）→ 进第 2 步。
- 缺失 → 按下面装，装完回到本节重查，不要凭"应该好了"继续。

`minidev` 是 npm 包，**先确认 Node/npm 可用**：

```bash
node -v && npm -v
```

- 没有 Node → 先装 Node（推荐官方安装包或 nvm），再装 minidev。不要用系统 Python/brew 的间接路径凑。
- 有 Node → 全局安装：

  ```bash
  npm i -g minidev
  ```

  装完的实际位置：`$(npm prefix -g)/lib/node_modules/minidev`，可执行软链在 `$(npm prefix -g)/bin/minidev`（本机为 `/usr/local/lib/node_modules/minidev` 与 `/usr/local/bin/minidev`）。

- 已有安装时**不要主动升级**。用户明确要求升级才用 `npm i -g minidev@latest`，并在升级后重跑本节验证。
- 官方文档站是单页应用，抓不到正文，安装命令以 npm 包名 `minidev` 为准；若用户配了内网 npm 源而装不上，报告该源不可达，不要偷偷切公网源。

## 2. 编译资源

`minidev` 自带编译器，缓存在 `~/.minidev/`（本机实测 356 MB）。首次使用或升级后如果 `build` 报编译器缺失（`spawn .../mini-pkg-builder ENOENT`），补下载：

```bash
minidev download-assets --with-compiler
```

- 这条只下载资源，不动项目，可以放心执行。
- 需要强制重下时加 `--clean-previous`。
- 磁盘紧张时报告实际占用（`du -sh ~/.minidev`）再让用户决定。

## 3. 授权

```bash
minidev app list --machine-output
```

- 返回非空数组 → 已授权，同时得到 `appId` / `appName`，进第 4 步。
- 报授权相关错误（未授权、密钥缺失、过期）→ 二选一，先问用户走哪条：

  **A. 快速授权（推荐，电脑级）**

  ```bash
  minidev login
  ```

  会给出授权链接或扫码入口。把链接/入口原样交还用户并结束本轮；**不要在同一轮阻塞等待**，用户完成后回到本节重跑探测。CLI 没有单独的生成二维码子命令，别去找 `minidev auth` 这类不存在的命令。

  **B. 身份密钥**

  用户在支付宝开放平台下载身份密钥后，后续命令显式带 `--identity-key-path <路径>`：

  ```bash
  minidev app list --machine-output --identity-key-path <路径>
  ```

  身份密钥在开放平台的应用详情里下载，具体菜单以控制台当前版本为准；找不到时直接用 A，不要凭猜测指路。

- 不要把密钥内容写进对话、插件文件、仓库或命令行历史；只需要路径。
- 多端场景（`ampe` / `amap` / `quark` 等）才需要 `-c <clientType>`，默认 `alipay`，不要替用户改端类型。

## 4. 只读验证

```bash
minidev app list --machine-output
```

成功标准：退出码 0 且 stdout 是 JSON 数组，至少含用户要操作的那个 `appId`。

这一步只证明**电脑已授权、能看到该应用**，不证明它有 `upload` / `audit` 权限。上传或提审权限要等对应命令真正返回才算，两者分开报告。

## 完成报告

报告：Node/npm 版本、`minidev` 版本与可执行路径、`~/.minidev` 资源占用与编译器是否就位、授权方式（快速授权还是密钥路径）、可见应用列表（appId + 名称，只列必要部分）。

缺失项要写清是哪一层：没 Node / 没 CLI / 没编译资源 / 没授权 / 无业务权限，并给出下一步该执行的那一条命令。
