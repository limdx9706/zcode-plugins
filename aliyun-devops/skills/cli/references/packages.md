# 制品仓库 Packages 命令

制品库、制品与制品版本。共 6 条。

> 参数以 `aliyun devops <命令> --help` 为准，下表只列**必填**参数。

## 制品（6 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-artifacts` | Query artifact information | --repo-id --repo-type |
| `list-repositories` | View repository information | — |
| `get-artifact` | View information of a single artifact | --id --repo-id --repo-type |
| `get-task` | View batch job | --id |
| `delete-artifact` | Delete a single artifact | --id --repo-id --repo-type |
| `delete-artifact-version` | Delete a single artifact version | --id --repo-id --repo-type --version-id |
