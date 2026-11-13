---
name: cli
description: Use the Yunxiao (云效) CLI for Codeup repos, Flow pipelines and deploys, Projex work items and effort hours, packages, and tests. Use when a task involves 云效, Codeup, 流水线, 工作项, 工时, 制品.
---

# 使用云效 CLI

所有云效操作通过 `aliyun devops` 完成。云效**没有独立 CLI**——它是阿里云 CLI 的产品插件（`aliyun-cli-devops`），入口只有 `aliyun`。若本回合没有可信的 setup 结果，先读并执行 `../setup/SKILL.md` 的检查。

## 命令选择

1. 加载令牌并确认身份：

   ```bash
   source ~/.yunxiao_env
   aliyun devops base-get-user-by-token
   ```

   `~/.zshrc` 里已有 `[ -f ~/.yunxiao_env ] && source ~/.yunxiao_env`，交互 shell 会自动加载；工具调起的非交互 shell 不一定，所以**显式 source**。失败信息是 `no credentials available for devops plugin`，说明没加载令牌，不是权限问题。

2. 用实时帮助发现命令，不凭记忆猜：

   ```bash
   aliyun devops --help                     # 442 条，按前缀分组
   aliyun devops --help-search <关键词>      # 搜命令，如 pipeline / workitem / artifact
   aliyun devops <命令> --help               # 看参数（二级子命令帮助不可用，到 <命令> 这一层为止）
   ```

   帮助里的 `Parameters:` 段是权威参数表。注意它是**按列宽折行**的：参数说明跨多行，还可能有 `structure:`（对象结构）和 `format:`（传值样例）两行，读的时候要一起看，只读首行会漏掉取值形态。

   帮助文本**可能滞后于实际实现**（实测过 `--help` 说"只支持 keyset"、二进制里却写着"省略即为页码分页"，而当前版本省略会直接报 `--pagination is required`）。取值拿不准时，用一个明显非法的值试一次，看服务端报什么，比读文档可靠。

3. 按模块查命令清单（每份含该模块全部命令、用途、必填参数）：

   | 模块 | 命令前缀 | 条数 | 参考 |
   | --- | --- | --- | --- |
   | 流水线 Flow | `flow-` | 86 | [references/flow.md](references/flow.md) |
   | 代码管理 Codeup | `codeup-` | 115 | [references/codeup.md](references/codeup.md) |
   | 项目协作 Projex | `projex-` | 76 | [references/projex.md](references/projex.md) |
   | 应用交付 AppStack | `app-stack-` | 93 | [references/app-stack.md](references/app-stack.md) |
   | 组织与成员 Base | `base-` | 37 | [references/base.md](references/base.md) |
   | 测试 Testhub | `test-hub-` | 26 | [references/test.md](references/test.md) |
   | 制品仓库 Packages | `packages-` | 6 | [references/packages.md](references/packages.md) |
   | 效能洞察 / 版本 | `insight-` `version` | 2 | 见下方「其余命令」 |

4. 解析返回：**形态不固定**。有时是裸数组，有时是 `{"result": ...}` / `{"workitem": ...}` / `{"versions": ...}`。用防御式解析，不要假定固定结构：

   ```bash
   aliyun devops projex-list-versions --id <projectId> 2>/dev/null | python3 -c "
   import sys, json
   d = json.load(sys.stdin)
   items = d if isinstance(d, list) else (d.get('versions') or d.get('result') or [])
   for v in items: print(v.get('id'), v.get('name'), v.get('status'))"
   ```

## 组织上下文

- 组织由 `--organization-id` 或环境变量 `ALIBABA_CLOUD_YUNXIAO_ORGANIZATION_ID` 决定；缺省时用令牌所在组织的默认值（组织 ID 缺失**不会报错**）。
- 换组织只需改这个参数/变量，不要换令牌。多组织并存时，在结果里标明用的是哪个组织。
- 分页：多数 `list-*` 命令是 `--page` / `--per-page`；AppStack 用 `--pagination` + `--next-token` 键集分页，`--current` / `--page-size` 是另一套，按 `--help` 为准。

## 常用链路

**跑一次流水线并跟到日志**

```bash
aliyun devops flow-list-pipelines --pipeline-name <关键词> --per-page 10
aliyun devops flow-create-pipeline-run --pipeline-id <pipelineId>            # 触发
aliyun devops flow-get-latest-pipeline-run --pipeline-id <pipelineId>        # 拿最新 runId 和状态
aliyun devops flow-list-pipeline-jobs --pipeline-id <id> --category DEPLOY   # category 目前只支持 DEPLOY
aliyun devops flow-get-pipeline-job-run-log --pipeline-id <id> --pipeline-run-id <runId> --job-id <jobId>
aliyun devops flow-stop-pipeline-job-run --pipeline-id <id> --pipeline-run-id <runId> --job-id <jobId>
aliyun devops flow-update-pipeline-run --pipeline-id <id> --pipeline-run-id <runId>   # 终止整次运行
```

`--params` 接一个 JSON 字符串，支持的键：`branchModeBranchs`（分支模式下要跑的分支）、`envs`（运行变量）、`runningBranchs`（以仓库地址为键指定分支）、`runningTags`（以仓库地址为键指定标签）。不知道当前流水线要带什么参数时，先 `flow-get-pipeline` 看定义，不要凭猜测拼。

流水线运行状态、任务状态、跳过/重试（`flow-skip-pipeline-job-run`、`flow-retry-pipeline-job-run`、`flow-rerun-pipeline-job-run`）都在 [references/flow.md](references/flow.md)。

**部署（AppStack 变更单）**

```bash
# 应用列表：--pagination 的合法值就是字面量 keyset（服务端会校验，乱值报 422 BadFormatForPagination）
# keyset 模式下 --order-by 只接受 id（传 name 同样 422）
aliyun devops app-stack-list-applications --order-by id --pagination keyset --per-page 10
# 翻页传上一页返回的 nextToken，首页不传
aliyun devops app-stack-list-applications --order-by id --pagination keyset --next-token <nextToken>

aliyun devops app-stack-list-change-orders --app-name <app> --current 1 --page-size 10
aliyun devops app-stack-create-change-order --app-name <app> --change-order-name <名称> \
  --envs '{"<环境名>":"<该环境的部署参数>"}' --type Deploy
aliyun devops app-stack-get-change-order --app-name <app> --change-order-sn <sn>
aliyun devops app-stack-list-change-order-job-logs --app-name <app> --change-order-sn <sn>
```

- `--envs` 是 **map**（帮助里给了 `structure: {<key>: string, ...}`）：键为环境名，值为该环境的部署参数；目前只支持**单环境**部署。
- `--type` 取值：`Deploy`（部署）、`Scale`（扩缩容）、`Rollback`（回滚）、`Destroy`（删除资源）。
- `--pagination`：取值 `keyset`。keyset 模式下**不返回 `total`**（实测为 `null`），靠 `nextToken` 判断是否还有下一页；拿不到数据时先确认该组织是否真的没有应用，而不是以为翻页错了。
- 变更单属对外发布链路，创建前必须确认应用、环境、版本与类型，并把 `--envs` 展开给人看。

**建工作项并登工时（Projex）**

```bash
aliyun devops projex-list-versions --id <projectId> --per-page 30        # 找进行中的版本
aliyun devops projex-create-workitem --space-id <projectId> --subject "<标题>" \
  --workitem-type-id <类型ID> --assigned-to <userId> --versions <versionId>
aliyun devops projex-create-effort-record --id <workitemId> --actual-time 7.5 \
  --gmt-start "2026-11-13" --gmt-end "2026-11-13" --work-type 研发 --description "<描述>"
aliyun devops projex-get-workitem --id <workitemId>                       # 回读验证
aliyun devops projex-list-effort-records --id <workitemId>
```

工时长描述建议先写到临时文件再 `--description "$(cat /tmp/xxx.txt)"`，避免长文本和引号在命令行里转义出错。写操作后**回读**确认，不靠命令返回自证成功。

**查制品**

```bash
aliyun devops packages-list-repositories --repo-types <类型>
aliyun devops packages-list-artifacts --repo-id <repoId> --repo-type <类型>
aliyun devops packages-get-artifact --repo-id <repoId> --repo-type <类型> --artifact-id <id>
```

## 其余命令

- `aliyun devops insight-list-queries` — 效能洞察查询列表。
- `aliyun devops version` — 打印插件版本。

## 写操作和数据边界

以下属于远端写操作，执行前展示目标、组织、资源和变更摘要并取得确认：

- **部署/发布**：`app-stack-create-change-order`、`*-execute-change-request-release-stage`、`app-stack-pass/refuse-release-stage-pipeline-validate`、`app-stack-update-env`。
- **流水线**：`flow-create-pipeline-run`（触发构建/部署）、`flow-update-pipeline`（改流水线定义）、`flow-stop/retry/skip-pipeline-job-run`、`flow-delete-pipeline`。
- **代码**：`codeup-merge-change-request`、`codeup-commit-multiple-files`、`codeup-delete-branch`、`codeup-archive-repository`、`codeup-transfer-repository`。
- **项目协作**：`projex-create/update/delete-workitem`、`projex-create-effort-record`（工时会进考核数据）、`projex-create-version`。
- **组织与权限**：`base-*-member`、`base-update-member`、`*-create-resource-member`（授权）、`flow-create-service-credential`（凭据）。
- **制品**：`packages-delete-artifact`、`packages-delete-artifact-version`（删除不可恢复）。

只读操作（`*-list-*`、`*-get-*`、`*-search-*`）直接执行。资源标识（projectId、pipelineId、workitemId、app 名、org-id）不明确时先查再问，不要从相似名称里猜。

### 凭证与隐私

- 令牌通过 `ALIBABA_CLOUD_YUNXIAO_ACCESS_TOKEN` 或 `--yunxiao-access-token` 传入；它**不是**阿里云 AK/SK，`aliyun devops` 也不读 `~/.aliyun/config.json`。
- 不要在对话、日志、命令参数或文件里回显令牌；不要用 `cat > ~/.yunxiao_env <<EOF` 这类方式把令牌写进命令（会留在会话记录里）。需要调整就让用户在自己的终端改。
- 结果里只保留必要字段：组织名、ID、状态、时间。不要整段复制接口返回。

## 完成报告

说明实际执行的命令、组织、命中的资源 ID 和结果；写操作未获确认时只给出待执行命令。失败时报告原始错误（令牌缺失、组织不匹配、权限不足、参数形态错误）并给出下一步，不自行换组织或重试写操作。

## 来源

- 命令清单与必填参数：本机 `aliyun devops --help` 全量枚举（441 条，2026-11-13，插件 v0.9.1 / aliyun 3.5.3）。
- 命令中文用途：`aliyun devops base-list-open-api-summaries` 返回的 OpenAPI 摘要，其余为帮助原文（已去掉 "available editions" 后缀）。
- 凭据与组织行为：`base-get-user-by-token`、`base-list-organizations` 实测，以及不加载令牌时的报错原文。
- 接口语义与参数取值形态：[云效 OpenAPI 文档](https://help.aliyun.com/zh/yunxiao/developer-reference/)。
