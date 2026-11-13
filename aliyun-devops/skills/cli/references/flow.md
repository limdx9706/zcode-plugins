# 流水线 Flow 命令

流水线、运行记录与日志、主机组与构建集群、变量与凭据。共 86 条。

> 参数以 `aliyun devops <命令> --help` 为准，下表只列**必填**参数。

## 流水线（16 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-pipeline-group-pipelines` | Get pipeline list under a pipeline group | --group-id |
| `list-pipeline-groups` | Get pipeline group list | — |
| `list-pipeline-relations` | Get pipeline association list | --pipeline-id --rel-object-type |
| `list-pipelines` | Get pipeline list | — |
| `get-pipeline` | Get pipeline details | --pipeline-id |
| `get-pipeline-group` | Get pipeline group | --group-id |
| `create-pipeline` | Create pipeline | --content --name |
| `create-pipeline-group` | Create pipeline group | --name |
| `update-pipeline` | Update pipeline | --content --name --pipeline-id |
| `update-pipeline-base-info` | Update pipeline basic information | --pipeline-id --pipeline-name |
| `update-pipeline-group` | Update pipeline group | --group-id --name |
| `add-pipeline-relations` | Add pipeline association | --pipeline-id --rel-object-ids --rel-object-type |
| `add-to-pipeline-group` | Add a pipeline to a pipeline group | --group-id --pipeline-ids |
| `delete-pipeline` | Delete pipeline | --pipeline-id |
| `delete-pipeline-group` | Delete pipeline group | --group-id |
| `delete-pipeline-relations` | Delete pipeline association | --pipeline-id --rel-object-id --rel-object-type |

## 流水线运行与日志（23 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `create-pipeline-run` | Run pipeline | --pipeline-id |
| `execute-pipeline-job-action` | Run follow-up action of pipeline job | --action-id --job-id --pipeline-id --pipeline-run-id |
| `execute-pipeline-job-run` | Manually run a pipeline job | --job-id --pipeline-id --pipeline-run-id |
| `rerun-pipeline-job-run` | Rerun a pipeline job | --job-id --pipeline-id --pipeline-run-id |
| `retry-pipeline-job-run` | Retry a pipeline job run | --job-id --pipeline-id --pipeline-run-id |
| `stop-pipeline-job-run` | Terminate pipeline job run | --job-id --pipeline-id --pipeline-run-id |
| `skip-pipeline-job-run` | Skip pipeline job run | --job-id --pipeline-id --pipeline-run-id |
| `pass-pipeline-validate` | Pass the manual checkpoint | --job-id --pipeline-id --pipeline-run-id |
| `refuse-pipeline-validate` | Reject manual gate | --job-id --pipeline-id --pipeline-run-id |
| `list-pipeline-job-historys` | Get execution history of a pipeline task | --category --identifier --pipeline-id |
| `list-pipeline-jobs` | Get pipeline execution jobs by job category | --category --pipeline-id |
| `list-pipeline-runs` | Get pipeline run list | --pipeline-id |
| `get-latest-pipeline-run` | Get the latest pipeline run information | --pipeline-id |
| `get-pipeline-artifact-url` | Get pipeline build artifact download URL | --file-name --file-path |
| `get-pipeline-debug-websocket-url` | Get pipeline task debug URL | --build-id --job-id --pipeline-id --pipeline-run-id |
| `get-pipeline-emas-artifact-url` | Get temporary download URL for pipeline emas build artifacts | --emas-job-instance-id --md5 --pipeline-id --pipeline-run-id --service-connection-id |
| `get-pipeline-job-run-log` | Query job run log | --job-id --pipeline-id --pipeline-run-id |
| `get-pipeline-job-step-log` | Get pipeline task step logs | --build-id --job-id --limit --offset --pipeline-id --pipeline-run-id --step-index |
| `get-pipeline-job-step-log-url` | Get download URL for pipeline task step logs | --build-id --job-id --pipeline-id --pipeline-run-id --step-index |
| `get-pipeline-job-steps` | Get pipeline task step list | --job-id --pipeline-id --pipeline-run-id |
| `get-pipeline-run` | Get pipeline run | --pipeline-id --pipeline-run-id |
| `get-pipeline-scan-report-url` | Get scan report download URL | --report-path |
| `update-pipeline-run` | Terminate pipeline run | --pipeline-id --pipeline-run-id |

## 主机组与构建集群（19 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-build-usage-details` | Query organization build usage details with pagination | --from-date --to-date |
| `list-host-groups` | Get host group list | — |
| `list-resource-members` | Get the resource member list | --resource-id --resource-type |
| `list-security-groups` | Query security group list | --aliyun-region-id --service-connection-id --vpc-id |
| `list-vpc-regions` | Query supported VPC region list | — |
| `list-vpc-zones` | Query availability zone list | --aliyun-region-id --service-connection-id |
| `list-vpcs` | Query VPC list | --aliyun-region-id --service-connection-id |
| `list-vswitchs` | Query switch list | --aliyun-region-id --service-connection-id --vpc-id --zone-id |
| `get-build-resource-network-status` | Check VPC build resources and network | --aliyun-region-id --biz-endpoint-type --service-connection-id --vpc-id --vswitch-ids --zone-ids |
| `get-host-group` | Get host group | --id |
| `create-build-group` | Create managed VPC build cluster | --machine-group-str |
| `create-host-group` | Create host group | --name --service-connection-id --type |
| `create-resource-member` | Insert resource member | --resource-id --resource-type --role-name --user-id |
| `update-host-group` | Update a host group | --id --name --service-connection-id --type |
| `update-resource-member` | Update resource member | --resource-id --resource-type --role-name --user-id |
| `update-resource-owner` | Transfer resource object owner | --new-owner-id --resource-id --resource-type |
| `delete-host-group` | Delete host group | --id |
| `delete-machine-group-machines` | Delete machine from host group | --id --machine-sns |
| `delete-resource-member` | Delete resource member | --resource-id --resource-type --user-id |

## 变量、凭据与服务连接（14 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-service-auths` | Get service authorization list | --service-auth-type |
| `list-service-connections` | Get service connection list | --service-connection-type |
| `list-service-credentials` | Get service certificate list | --service-credential-type |
| `list-variable-groups` | Get variable group list | — |
| `get-variable-group` | Get variable group | --id |
| `create-service-auth` | Create service authorization | --service-auth-type |
| `create-service-connection` | Create service connection | — |
| `create-service-credential` | Create service credential | --name --password --type --username |
| `create-ssh-key` | Create enterprise public key | — |
| `create-variable-group` | Create variable group | --name --variables |
| `update-kubernetes-kube-config` | Update the Kubernetes cluster name and config file | --id --kube-config --name |
| `update-ssh-key` | Reset the enterprise public key | — |
| `update-variable-group` | Update variable group | --id --name --variables |
| `delete-variable-group` | Delete variable group | --id |

## 标签（8 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-flow-tag-groups` | Get label category list | — |
| `get-flow-tag-group` | Get label category | --id |
| `create-flow-tag` | Create label | --color --flow-tag-group-id --name |
| `create-flow-tag-group` | Create label category | --name |
| `update-flow-tag` | Update label | --color --flow-tag-group-id --id --name |
| `update-flow-tag-group` | Update label category | --id --name |
| `delete-flow-tag` | Delete label | --id |
| `delete-flow-tag-group` | Delete label category | --id |

## VM 部署单（6 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `retry-vm-deploy-machine` | Retry machine deployment | --deploy-order-id --machine-sn --pipeline-id |
| `resume-vm-deploy-order` | Resume deploy order run | --deploy-order-id --pipeline-id |
| `stop-vm-deploy-order` | Terminate machine deployment | --deploy-order-id --pipeline-id |
| `skip-vm-deploy-machine` | Skip host deployment | --deploy-order-id --machine-sn --pipeline-id |
| `get-vm-deploy-machine-log` | Query machine deployment log | --deploy-order-id --machine-sn --pipeline-id |
| `get-vm-deploy-order` | Get deploy order details | --deploy-order-id --pipeline-id |
