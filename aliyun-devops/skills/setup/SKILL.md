---
name: setup
description: Check or install the Yunxiao CLI plugin (aliyun-cli-devops), provide a personal access token, select the organization, and verify with one read-only call before any 云效 operation.
---

# 设置云效 CLI

只做本机依赖准备和只读验证。已有环境直接复用；不因为再次运行 setup 就重装插件、重置令牌或切换组织。

## 1. 检查 CLI 与云效插件

```bash
command -v aliyun
aliyun version
aliyun devops version
```

- 云效没有独立 CLI，`aliyun devops` 由产品插件 `aliyun-cli-devops` 提供（manifest 里 `productCode: devops`、`profileRequired: false`、`minCliVersion: 3.3.16`）。插件装在 `~/.aliyun/plugins/aliyun-cli-devops/`。
- `aliyun devops` 报"不是有效命令"时说明插件没装。安装会下载并执行上游代码，需取得用户确认后执行：

  ```bash
  aliyun plugin install --names aliyun-cli-devops
  ```

- `aliyun --help` 的根帮助里看不到 `devops` 属正常：398 个产品只显示前 75 个。用 `aliyun --help-search devops` 确认。

## 2. 令牌

云效用**个人访问令牌**（PAT，`pt-` 开头），不是阿里云 AccessKey。获取方式：云效 → 个人设置 → 个人访问令牌，按需勾选功能模块权限，**令牌只在创建时显示一次**。

两种传入方式，优先环境变量：

```bash
export ALIBABA_CLOUD_YUNXIAO_ACCESS_TOKEN=<令牌>   # 建议由用户写进 ~/.yunxiao_env
aliyun devops <命令> --yunxiao-access-token <令牌>  # 临时传入
```

- 推荐落到 `~/.yunxiao_env`（`chmod 600`），`~/.zshrc` 里加 `[ -f ~/.yunxiao_env ] && source ~/.yunxiao_env` 实现交互 shell 自动加载。
- **不要**用 `cat > ~/.yunxiao_env <<EOF` 这类把令牌写进命令行的方式：命令会留在会话记录和 shell history 里。让用户自己在终端编辑，或用 `read -s` 读入。
- 报 `no credentials available for devops plugin` 就是没加载令牌；它同时列出了两种传入方式，不是权限错误。

## 3. 组织

```bash
source ~/.yunxiao_env
aliyun devops base-list-organizations
```

确认目标组织后，把 ID 写进同一个 env 文件：

```bash
export ALIBABA_CLOUD_YUNXIAO_ORGANIZATION_ID=<组织ID>
```

- 组织 ID 缺失不会报错，会退到令牌归属的默认组织；多组织环境下这条**必须显式设置**，否则可能写到错的租户。
- 临时切组织用 `--organization-id <id>`，不要换令牌。

## 4. 验证身份

```bash
aliyun devops base-get-user-by-token
```

成功以退出码 0 且返回里有用户标识为准。只报告必要的用户名/ID，不打印令牌或完整响应。

## 5. 只读验证

```bash
aliyun devops codeup-list-repositories --per-page 1
```

看到代码库列表即链路通。这一步只证明令牌可用且能看到组织资源，**不证明**对某个流水线、项目或应用有写权限——那要等具体写操作返回才算。

## 完成条件

报告：CLI 与插件版本、令牌是否已加载（不外显）、当前组织、第 5 步是否拿到数据。任何一步失败都说明具体错误，不要把"插件已装"当成"CLI 可用"，也不要凭用户一句"配好了"跳过第 5 步。
