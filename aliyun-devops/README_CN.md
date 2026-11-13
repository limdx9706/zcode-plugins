# 云效 CLI 插件

[English](./README.md)

本插件是云效 CLI（`aliyun devops`）的轻量 Skill 封装。云效没有独立 CLI，它是阿里云 CLI 的产品插件 `aliyun-cli-devops`，入口只有 `aliyun`。插件只调用用户本机已安装的 CLI，不内置 CLI 二进制、凭证、MCP 或远程服务。

封装由 lidongxin 维护，版本 0.1.0；这不是阿里云官方发布的插件。上游 CLI、文档和商标仍归阿里云所有。

## 快速 setup

运行 `/aliyun-devops:setup`。先复用已有插件与令牌：`aliyun devops version` 确认插件，`aliyun devops base-get-user-by-token` 确认身份。插件缺失时才 `aliyun plugin install --names aliyun-cli-devops`。

云效使用**个人访问令牌**（`pt-` 开头），不是阿里云 AK/SK，`aliyun devops` 也不读 `~/.aliyun/config.json`。令牌放 `~/.yunxiao_env`（600），`~/.zshrc` 里 `[ -f ~/.yunxiao_env ] && source ~/.yunxiao_env` 自动加载。组织由 `ALIBABA_CLOUD_YUNXIAO_ORGANIZATION_ID` 或 `--organization-id` 指定；缺失时不报错，会退到令牌的默认组织。

## Skills

| Skill | 入口 | 用途 |
| --- | --- | --- |
| `setup` | `/aliyun-devops:setup` | 检查插件、配置令牌与组织、跑一次只读验证 |
| `cli` | 自动按上下文触发 | 流水线运行与日志、部署变更单、Codeup 仓库与 MR、工作项与工时、制品、测试 |

## 覆盖范围

按 `aliyun devops --help` 实际枚举，共 441 条子命令，全部按模块整理进 `skills/cli/references/`：

| 模块 | 前缀 | 条数 |
| --- | --- | --- |
| 流水线 Flow | `flow-` | 86 |
| 代码管理 Codeup | `codeup-` | 115 |
| 项目协作 Projex | `projex-` | 76 |
| 应用交付 AppStack | `app-stack-` | 93 |
| 组织与成员 Base | `base-` | 37 |
| 测试 Testhub | `test-hub-` | 26 |
| 制品仓库 Packages | `packages-` | 6 |
| 效能洞察 / 版本 | `insight-` `version` | 2 |

每份参考列命令、用途和**必填**参数；完整参数以 `aliyun devops <命令> --help` 为准。

## 安全边界

- 跑流水线、建变更单、合并 MR、改工作项、登工时、删制品都属远端写操作，会先展示组织、资源 ID 和变更摘要并请求确认。
- 令牌不写进命令行、不落 shell history、不在对话里回显；推荐 `~/.yunxiao_env` + 600。
- 组织 ID 不明时先 `base-list-organizations` 确认，不凭相似名称猜租户。
- 返回形态不固定（裸数组 / `result` / 具名键），解析用防御式写法；只保留必要字段。

## 上游文档

- [云效 OpenAPI](https://help.aliyun.com/zh/yunxiao/developer-reference/)
- [个人访问令牌](https://help.aliyun.com/zh/yunxiao/developer-reference/obtain-personal-access-token)
- [阿里云 CLI](https://github.com/aliyun/aliyun-cli)

启用或更新插件后请新建 ZCode session，确保 Skill 清单刷新。
