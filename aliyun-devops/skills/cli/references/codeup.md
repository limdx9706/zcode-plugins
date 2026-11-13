# 代码管理 Codeup 命令

仓库、分支与提交、合并请求（MR）、标签与 Webhook。共 115 条。

> 参数以 `aliyun devops <命令> --help` 为准，下表只列**必填**参数。

## 仓库（24 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-group-members` | Query code group member list | --group-id |
| `list-group-repositories` | Query code repository list under a code group | --group-id |
| `list-namespaces` | Query code group list | — |
| `list-repositories` | Query code repository list | — |
| `list-repository-members` | Query code repository member list | --repository-id |
| `list-template-repositories` | Query template code repository list | --template-type |
| `list-user-resources` | Query resources the user has permission on | — |
| `get-namespace` | Query code group information | --namespace-id |
| `get-repository` | Query code repository | --repository-id |
| `create-group` | Create code group | --name --path |
| `create-group-member` | Add code group member | --access-level --group-id |
| `create-repository` | Create code repository | --name --path |
| `create-repository-member` | Add code repository member | --access-level --repository-id |
| `update-group` | Update code group | --group-id |
| `update-group-member` | Change the permissions of a code group member | --access-level --group-id --user-id |
| `update-repository` | Update code repository | --repository-id |
| `update-repository-member` | Change the permissions of a code repository member | --access-level --repository-id --user-id |
| `archive-repository` | Archive a code repository | --repository-id |
| `delete-group-member` | Remove code group member | --group-id --user-id |
| `delete-group-team-member` | Remove code group department member | --group-id --team-id |
| `delete-repository` | Delete code repository | --repository-id |
| `delete-repository-member` | Remove code repository member | --repository-id --user-id |
| `delete-repository-team-member` | Remove code repository department member | --repository-id --team-id |
| `transfer-repository` | Transfer code repository | --group-id --repository-id |

## 分支、提交与保护规则（30 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-branches` | Query branch list | --repository-id |
| `list-commit-statuses` | Query commit status list | --repository-id --sha |
| `list-commits` | Query commit list | --ref-name --repository-id |
| `list-files` | Query file tree | --repository-id |
| `list-protected-branches` | Query protected branch list | --repository-id |
| `list-push-rules` | Query push rule list | --repository-id |
| `list-tags` | Query label list | --repository-id |
| `get-branch` | Query branch information | --branch-name --repository-id |
| `get-commit` | Query commit information | --repository-id --sha |
| `get-compare` | Query code comparison content | --from --repository-id --to |
| `get-file-blame` | Get file blame information | --file-path --ref --repository-id |
| `get-file-blobs` | Query file content | --file-path --ref --repository-id |
| `get-protected-branch` | Query protected branch | --id --repository-id |
| `get-push-rule` | Query push rule | --push-rule-id --repository-id |
| `create-branch` | Create branch | --branch --ref --repository-id |
| `create-commit-comment` | Add a comment to a single commit | --content --repository-id --sha |
| `create-commit-status` | Create commit status | --repository-id --sha --state |
| `create-file` | Create file | --branch --commit-message --content --encoding --file-path --repository-id |
| `create-protected-branch` | Create protected branch | --branch --repository-id |
| `create-push-rule` | Create push rule | --repository-id |
| `create-tag` | Create label | --ref --repository-id --tag-name |
| `update-file` | Update file content | --branch --commit-message --content --file-path --repository-id |
| `update-protected-branch` | Update protected branch | --branch --id --repository-id |
| `update-push-rule` | Update push rule | --push-rule-id --repository-id |
| `delete-branch` | Delete branch | --branch-name --repository-id |
| `delete-file` | Delete file | --branch --commit-message --file-path --repository-id |
| `delete-protected-branch` | Delete protected branch | --id --repository-id |
| `delete-push-rule` | Delete push rule | --push-rule-id --repository-id |
| `delete-tag` | Delete label | --repository-id --tag-name |
| `commit-multiple-files` | Commit multiple file changes | --repository-id |

## 合并请求与评审（25 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `close-change-request` | Close merge request | --local-id --repository-id |
| `list-change-request-ci-check-list` | Query merge request CI automated check list | --local-id --patch-set-biz-id --repository-id |
| `list-change-request-patch-sets` | Query merge request version list | --local-id --repository-id |
| `list-change-requests` | Query merge request list | — |
| `list-check-runs` | Query run check list | --ref --repository-id |
| `list-merge-request-comments` | Query comment list | --local-id --repository-id |
| `list-merge-requests` | Query merge request list (old) | — |
| `get-change-request` | Query merge request | --local-id --repository-id |
| `get-change-request-labels` | Get labels of a merge request | --local-id --repository-id |
| `get-change-request-tree` | Query the changed file tree of a merge request | --from-patch-set-id --local-id --repository-id --to-patch-set-id |
| `get-check-run` | Query run check | --check-run-id --repository-id |
| `get-merge-request` | Query merge request (old) | --iid --repository-id |
| `create-ai-review-run` | Trigger AI code review | --local-id --repository-id |
| `create-change-request` | Create merge request | --repository-id --source-branch --source-project-id --target-branch --target-project-id --title |
| `create-change-request-comment` | Create merge request comment | --comment-type --content --draft --local-id --patchset-biz-id --repository-id --resolved |
| `create-check-run` | Create run check | --head-sha --name --repository-id |
| `update-change-request` | Update merge request basic information | --local-id --repository-id |
| `update-change-request-comment` | Update merge request comment | --comment-biz-id --local-id --repository-id |
| `update-change-request-related-person` | Update merge request stakeholders | --local-id --repository-id --type |
| `update-check-run` | Update run check | --check-run-id --repository-id |
| `delete-change-request-comment` | Delete merge request comment | --comment-biz-id --local-id --repository-id |
| `merge-change-request` | Merge a merge request | --local-id --merge-type --repository-id |
| `reopen-change-request` | Reopen a merge request | --local-id --repository-id |
| `review-change-request` | Review a merge request | --local-id --repository-id |
| `sync-ai-review-run` | Sync AI code review run status | --local-id --repository-id |

## 标签与 Webhook（11 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-web-hook-history` | Query Webhook send history | --repository-id --web-hook-id |
| `list-web-hooks` | Query Webhook list | --repository-id |
| `get-project-labels` | Get the project category list | --repository-id |
| `get-web-hook` | Query WebHook | --hook-id --repository-id |
| `create-project-label` | Create project label | --label-color --label-name --repository-id |
| `create-web-hook` | Create Webhook | --repository-id --url |
| `update-project-label` | Update project category label | --label-id --repository-id |
| `update-web-hook` | Update a WebHook | --hook-id --repository-id --url |
| `attach-labels-to-change-request` | Associate label with merge request | --label-id-list --local-id --repository-id |
| `delete-project-label` | Delete project category | --label-id --repository-id |
| `delete-web-hook` | Delete WebHook | --hook-id --repository-id |

## 密钥与成员（9 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-ssh-keys` | Query SSH Key list | — |
| `list-user-ssh-keys` | Query SSH Key list of the specified user | --user-id |
| `get-member-https-clone-username` | Query user clone account | --user-id |
| `get-ssh-key` | Query SSH Key | --key-id |
| `create-deploy-key` | Create deploy key | --key --repository-id --title |
| `create-ssh-key` | Create SSH Key | --key --key-scope --title |
| `delete-ssh-key` | Delete SSH Key | --key-id |
| `disable-deploy-key` | Disable deploy key | --key-id --repository-id |
| `enable-deploy-key` | Start deploy key | --key-id --repository-id |

## 代码质量（16 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-quality-check-plan` | Query detection plan list | — |
| `list-quality-check-rule-package` | Query detection rule package list | — |
| `get-fix-issue-record-context` | Get smart fix record context | --record-id --repository-id |
| `get-quality-check-plan` | Query detection plan details | --biz-no |
| `get-quality-check-task` | Query detection tasks associated with code repositories | --repository-id |
| `get-quality-scan-profile` | Query code repository scan configuration | --repository-id |
| `create-quality-check-plan` | Create code inspection plan | --match-languages --plan-name |
| `create-quality-check-task` | Create code inspection task | --plan-biz-no --repository-id |
| `create-quality-scan-record` | Create scan record | --branch --repository-id --scan-method --scan-object |
| `update-fix-issue-record` | Write back intelligent fix result | --record-id --repository-id |
| `update-quality-check-plan` | Update inspection plan | --biz-no |
| `update-quality-check-task` | Update inspection task | --biz-no --repository-id |
| `update-quality-scan-result` | Update scan result | --check-task-issues --check-task-record --repository-id |
| `delete-quality-check-plan` | Delete inspection plan | --biz-no |
| `delete-quality-check-task` | Delete inspection task | --biz-no --repository-id |
| `sync-scan-pipeline-steps` | Sync scan pipeline step configuration | --job-id --pipeline-id --repository-id |
