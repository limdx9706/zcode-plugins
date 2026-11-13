# 项目协作 Projex 命令

工作项、工时、版本与迭代、项目成员。共 76 条。

> 参数以 `aliyun devops <命令> --help` 为准，下表只列**必填**参数。

## 工作项（31 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-all-workitem-types` | Get the list of all work item types in an organization | — |
| `list-workitem-activities` | Get work item activities | --id |
| `list-workitem-attachments` | Get work item attachment list | --id |
| `list-workitem-comments` | Get work item comment list | --id |
| `list-workitem-ext-relation-records` | Query external resources linked to a work item | --category --id |
| `list-workitem-relation-records` | Get work item related items | --id --relation-type |
| `list-workitem-relation-workitem-types` | Get list of work item types that can be associated with a work item | --id |
| `list-workitem-testcase-relation-records` | Query test cases linked to a work item | --id |
| `list-workitem-types` | Get the work item type list in a project | --category --id |
| `get-workitem` | Get work item | --id |
| `get-workitem-file` | Get work file information | --id --workitem-id |
| `get-workitem-type` | Get work item type details | --id |
| `get-workitem-type-description-config` | Get the description template configuration of a work item type in a project | --project-id --workitem-type-id |
| `get-workitem-type-field-config` | Get the field configuration of a work item type in a project | --id --project-id |
| `get-workitem-workflow` | Get status list of a work item workflow | --id --project-id |
| `get-workitem-workflow-info` | Get work item workflow information | --id --x-tenant-id |
| `search-workitems` | Search work items | --category --space-id |
| `create-starred-workitem` | Favorite work item | --id |
| `create-workitem` | Create work item | --assigned-to --space-id --subject --workitem-type-id |
| `create-workitem-attachment` | Upload work item attachment | --file --id |
| `create-workitem-comment` | Create work item comment | --content --id |
| `create-workitem-ext-relation-record` | Create work item association with external resource | --category --id |
| `create-workitem-relation-record` | Create work item association | --id |
| `create-workitem-testcase-relation-record` | Create work item association with test case | --id --testcase-id |
| `update-custom-field` | Update custom field | --id |
| `update-workitem` | Update work item | --id |
| `delete-starred-workitem` | Unfavorite work item | --id |
| `delete-workitem` | Delete work item | --id |
| `delete-workitem-ext-relation-record` | Delete work item linked external resource | --id --relation-record-id |
| `delete-workitem-relation-record` | Delete work item linked item | --id |
| `delete-workitem-testcase-relation-record` | Delete work item linked test case | --id --relation-record-id |

## 工时（7 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-current-user-effort-records` | Get a user's actual work hours details; the interval between end time and start time cannot exceed 6 months | --end-date --start-date |
| `list-effort-records` | Get actual work hours details | --id |
| `list-estimated-efforts` | Get estimated work hours details | --id |
| `create-effort-record` | Log actual work hours | --actual-time --gmt-end --gmt-start --id |
| `create-estimated-effort` | Log estimated work hours | --description --id --owner --spent-time |
| `update-effort-record` | Update logged actual work hours | --actual-time --gmt-end --gmt-start --id --workitem-id |
| `update-estimated-effort` | Update logged estimated work hours | --description --id --owner --spent-time --workitem-id |

## 版本、迭代与里程碑（13 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-milestones` | Get the milestone list | --id |
| `list-program-versions` | Get the program version list | --id |
| `list-sprints` | Get the sprint list | --id |
| `list-versions` | Get version list | --id |
| `get-sprint` | Get a sprint | --id --project-id |
| `create-milestone` | Create milestone | --assigned-to --id --plan-end-date --subject |
| `create-sprint` | Create sprint | --id --name --owners |
| `create-version` | Create version | --id --name --owners |
| `update-milestone` | Update milestone | --id --project-id |
| `update-sprint` | Update sprint | --id --name --project-id |
| `update-version` | Update version | --id --name --project-id |
| `delete-milestone` | Delete milestone | --id --project-id |
| `delete-version` | Delete version | --id --project-id |

## 项目与成员（25 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-all-project-roles` | Get the list of all project roles in an organization | — |
| `list-labels` | Get label list | --id |
| `list-project-members` | 获取项目成员列表 | --id |
| `list-project-next-statuses` | 获取项目可流转状态 | --id |
| `list-project-roles` | 获取项目角色列表 | --id |
| `list-project-templates` | Get a project template | — |
| `get-project` | 获取项目 | --id |
| `get-project-ai-config` | 获取项目 AI 配置 | --id |
| `get-project-field-config` | 获取项目自定义字段配置 | --id |
| `get-project-template-field-config` | Get project template field configuration | --id |
| `search-programs` | 搜索项目集 | — |
| `search-projects` | 搜索项目 | — |
| `create-archived-project` | 归档项目 | --id |
| `create-label` | Create label | --color --id --name |
| `create-project` | 创建项目 | --custom-code --name --scope --template-id |
| `create-project-member` | 添加项目成员 | --id --role-id --user-ids |
| `create-project-role` | Add project role | --id --role-ids |
| `create-starred-project` | 收藏项目 | --id |
| `update-label` | Update label | --id --project-id |
| `update-project` | 更新项目 | --id |
| `delete-archived-project` | 取消归档项目 | --id |
| `delete-project` | 删除项目 | --id --name |
| `delete-project-member` | 删除项目成员 | --id --user-id |
| `delete-project-role` | Delete project role | --id --role-ids |
| `delete-starred-project` | 取消收藏项目 | --id |
