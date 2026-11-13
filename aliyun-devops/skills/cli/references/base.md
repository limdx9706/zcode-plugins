# 组织与成员 Base 命令

组织、成员与部门、角色、自动化任务。共 37 条。

> 参数以 `aliyun devops <命令> --help` 为准，下表只列**必填**参数。

## 组织（4 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-bind-info` | 列出组织成员特定类型的绑定信息 | --bind-type |
| `list-org-domains` | 查询企业域名列表 | — |
| `list-organizations` | 查询组织列表 | — |
| `get-bind-info` | 获取单个成员绑定信息 | --member-id |

## 成员与部门（10 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-department-ancestors` | 查询组织内部门的所有父部门列表 | --id |
| `list-departments` | 查询组织部门列表 | — |
| `list-members` | 查询成员列表 | — |
| `get-department` | 查询组织部门信息 | --id |
| `get-member` | 查询成员信息 | --id |
| `get-user-by-token` | 根据个人访问令牌查询对应用户信息 | — |
| `search-members` | 搜索成员列表 | — |
| `update-member` | Update member information | --id |
| `register-member` | Register organization member | --name --password --username |
| `read-member-by-user` | 查询成员信息 | --user-id |

## 角色（2 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-roles` | 查询组织角色列表 | — |
| `get-role` | 查询角色信息 | --id |

## 自动化任务（19 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `execute-automation-scheduled-task` | 立即运行定时任务 | --name |
| `list-automation-event-task-runs` | 查询事件任务执行记录 | --name |
| `list-automation-event-tasks` | 查询事件任务列表 | — |
| `list-automation-event-types` | 查询事件类型目录 | — |
| `list-automation-scheduled-task-runs` | 查询定时任务执行记录 | --name |
| `list-automation-scheduled-tasks` | 查询定时任务列表 | — |
| `get-automation-event-task` | 查询事件任务详情 | --name |
| `get-automation-event-type-fields` | 查询事件字段目录 | --type |
| `get-automation-scheduled-task` | 查询定时任务详情 | --name |
| `get-automation-scheduled-task-capabilities` | 查询定时任务渠道可用性 | — |
| `dry-run-automation-event-task` | 预演事件任务 | --event --form |
| `create-automation-event-task` | 创建事件任务 | --display-name --event-types --prompt |
| `create-automation-scheduled-task` | 创建定时任务 | --prompt --schedule --title |
| `update-automation-event-task` | 更新事件任务 | --display-name --event-types --name --prompt |
| `update-automation-event-task-enabled` | 启用或暂停事件任务 | --enabled --name |
| `update-automation-scheduled-task` | 更新定时任务 | --name --prompt --schedule --title |
| `update-automation-scheduled-task-enabled` | 启用或暂停定时任务 | --enabled --name |
| `delete-automation-event-task` | 删除事件任务 | --name |
| `delete-automation-scheduled-task` | 删除定时任务 | --name |

## OpenAPI 元数据（2 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-open-api-summaries` | Query OpenAPI summary list | — |
| `get-open-api-schema` | Query the invocation Schema of a single OpenAPI | --app-identity --operation-id |
