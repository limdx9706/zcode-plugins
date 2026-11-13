# 应用交付 AppStack 命令

应用、环境、变更单、发布流程、部署组。共 93 条。

> 参数以 `aliyun devops <命令> --help` 为准，下表只列**必填**参数。

## 应用（18 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-application-members` | List application members | --app-name --current --page-size |
| `list-application-sources` | List application source details with pagination | --app-name |
| `list-applications` | List application details with pagination | --order-by --pagination |
| `get-application` | Get application details | --app-name |
| `search-app-tag` | Query application labels | — |
| `search-app-templates` | Search application templates | — |
| `create-app-members` | Add application member | --app-name --player-list --role-names |
| `create-app-tag` | Create application label | --color --name |
| `create-application` | Create application | --name |
| `create-application-source` | Create application source | --app-name --biz-body |
| `update-app-member` | Update application member | --app-name --player --role-names |
| `update-app-tag` | Update application label | --biz-body --name |
| `update-application` | Update application | --app-name |
| `attach-template` | Associate an application with an application template | --app-template-name --name |
| `delete-app-member` | Delete application member | --app-name --subject-id --subject-type |
| `delete-app-tag` | Delete application label | --name |
| `delete-application` | Delete application | --app-name |
| `detach-template` | Unbind an application template from an application | --name |

## 环境（9 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-environments` | List environment details with pagination | --app-name |
| `get-env-variable-groups` | Get details of variable groups bound to an environment | --app-name --env-name |
| `get-environment` | Get environment details | --app-name --env-name |
| `create-environments` | Create environment | --app-name --label-list --name --resource-pool-name --resource-type |
| `update-env` | Update environment | --app-name --env-name --resource-type |
| `update-env-computation-resource` | Associate compute resources with an environment | --app-name --deploy-group-name --env-name --resource-type |
| `lock-env` | Lock an environment | --app-name --env-name |
| `un-lock-env` | Unlock environment | --app-name --env-name |
| `delete-env` | Delete environment | --app-name --env-name |

## 变更单与变更请求（39 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `execute-change-request-release-stage` | Execute a development stage pipeline | --app-name --release-stage-sn --release-workflow-sn |
| `execute-job-action` | Operate environment deploy order | --action-type --app-name --change-order-sn --job-sn |
| `retry-change-request-stage-pipeline` | Retry a development stage run | --app-name --execution-number --job-id --release-stage-sn --release-workflow-sn |
| `skip-change-request-stage-pipeline` | Skip development stage run | --app-name --execution-number --job-id --release-stage-sn --release-workflow-sn |
| `cancel-change-request` | Close change request | --app-name --sn |
| `cancel-execution-release-stage` | Cancel the development stage pipeline run | --app-name --execution-number --release-stage-sn --release-workflow-sn |
| `close-change-request` | Complete a change request | --app-name --sn |
| `pass-release-stage-pipeline-validate` | Pass the manual checkpoint | --app-name --execution-number --job-id --release-stage-sn --release-workflow-sn |
| `refuse-release-stage-pipeline-validate` | Reject manual gate | --app-name --execution-number --job-id --release-stage-sn --release-workflow-sn |
| `list-all-release-stage-briefs` | Get summaries of all development stages under a development process | --app-name --release-workflow-sn |
| `list-all-release-workflow-briefs` | List summaries of all development processes under an application | --app-name |
| `list-all-release-workflows` | List all development processes under an application | --app-name |
| `list-app-change-requests` | Search change request list | --app-name |
| `list-app-orchestration` | Get application orchestration list | --app-name |
| `list-app-release-stage-execution-integrated-metadata` | Query integrated change request information of a development stage execution record | --app-name --execution-number --release-stage-sn --release-workflow-sn |
| `list-app-release-stage-runs` | Batch query development stage execution records | --app-name --release-stage-sn --release-workflow-sn |
| `list-attached-change-requests` | List change requests associated with a release | --current --page-size --release-sn --system-name |
| `list-change-order-job-logs` | Query environment deploy order log | --app-name --change-order-sn --current --job-sn --page-size |
| `list-change-order-versions` | View deploy order version list | --app-name --page-size |
| `list-change-orders` | Query deploy order list | --app-name --current --page-size |
| `list-change-orders-by-origin` | Query deploy orders by creation source | --origin-id --origin-type |
| `list-change-request-executions` | Query change request development process run records | --app-name --release-stage-sn --release-workflow-sn --sn |
| `list-change-request-work-items` | Query work items linked to a change request | --app-name --sn |
| `list-release-stage-flow-change-requests` | Query release stage integration metadata | --app-name --release-stage-sn --release-workflow-sn |
| `get-app-orchestration` | Get application orchestration details | --app-name --sn |
| `get-app-orchestration-export` | Export an application orchestration | --app-name --render-type --sha --suitable-resource-type |
| `get-app-release-stage-execution-pipeline-job-log` | Query job run log of a development stage pipeline | --app-name --execution-number --job-id --release-stage-sn --release-workflow-sn |
| `get-change-order` | Read the materials and work order status used by the deploy order | --app-name --change-order-sn |
| `get-change-request-audit-items` | List approval items associated with a change request | --app-name --ref-type --sn |
| `get-latest-orchestration` | Get the latest available orchestration of an environment | --app-name --env-name |
| `get-release-stage-pipeline-run` | Get development stage pipeline runs | --app-name --execution-number --release-stage-sn --release-workflow-sn |
| `get-release-workflow-stage` | Get development stage details | --app-name --release-stage-sn --release-workflow-sn |
| `create-app-orchestration` | Create application orchestration | --app-name --template-id --template-type |
| `create-change-order` | Create deploy order | --app-name --change-order-name --envs --type |
| `create-change-request` | Create change request | --app-code-repo-sn --app-name --auto-delete-branch-when-end --branch-name --create-branch --title |
| `update-app-orchestration` | Update application orchestration | --app-name --name --sn |
| `update-release-stage` | Update development stage | --app-name --label-list --name --release-stage-sn --release-workflow-sn --variable-groups |
| `update-release-stage-flow` | Update release stage YAML configuration | --app-name --flow --release-stage-sn --release-workflow-sn |
| `delete-app-orchestration` | Delete application orchestration | --app-name --sn |

## 部署组与主机（6 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `get-deployment-revision-info` | Read workload version information | --app-name --env-name --name --namespace --revision |
| `get-machine-deploy-log` | Query host deployment log | --machine-sn --tunnel-id |
| `add-host-list-to-deploy-group` | Add hosts to a deployment group | --group-name --host-sns --instance-name |
| `add-host-list-to-host-group` | Add hosts to a host cluster | --host-sns --instance-name |
| `delete-host-list-from-deploy-group` | Remove hosts from a deployment group | --group-name --host-sns --instance-name |
| `delete-host-list-from-host-group` | Remove hosts from a host cluster | --host-sns --instance-name |

## 变量（11 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-global-vars` | Query global variable group list | --current --page-size |
| `get-app-variable-groups` | Get variable group list details of an application | --app-name |
| `get-app-variable-groups-revision` | Get variable group version of an application | --app-name |
| `get-global-var` | Query global variable group | --name |
| `get-variable-group` | Query variable group details | --app-name --variable-group-name |
| `create-global-var` | Create global variable group | --display-name --name |
| `create-variable-group` | Create variable group | --app-name --from-revision-sha |
| `update-global-var` | Update global variable group | --content --from-revision-sha --name |
| `update-variable-group` | Update variable group | --app-name --from-revision-sha --variable-group-name |
| `delete-global-var` | Delete global variable group | --name |
| `delete-variable-group` | Delete variable group | --app-name --variable-group-name |

## 运行态排查（5 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `get-kubernetes-object-info` | Read the materials and work order status used by the deploy order | --kind --name --namespace --resource-path |
| `get-pod-container-log` | Read Pod container logs | --container --name --namespace --resource-path --tailing-lines |
| `get-pod-info` | Query Pod information | --name --namespace --resource-path |
| `find-task-operation-log` | Query deployment job execution logs, which usually contain scheduling details of the downstream deployment engine | --app-name --change-order-sn --job-sn --stage-sn --task-sn |
| `update-resource-instance` | Update resource instance | --kubeconfig --name |

## Webhook（5 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-app-stack-webhook-history` | Get AppStack Webhook execution history | --sn |
| `list-app-stack-webhooks` | List AppStack Webhooks | --channel-type --scope-id --scope-type |
| `create-app-stack-webhook` | Create AppStack Webhook | --description --events --scope-id --scope-type --spec --type |
| `update-app-stack-webhook` | Update an AppStack Webhook | --biz-body --sn |
| `delete-app-stack-webhook` | Delete AppStack Webhook | --sn |
