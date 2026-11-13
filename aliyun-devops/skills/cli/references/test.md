# 测试 Testhub 命令

测试计划、用例、测试结果。共 26 条。

> 参数以 `aliyun devops <命令> --help` 为准，下表只列**必填**参数。

## 测试计划（8 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-test-plan` | Get test plan list | — |
| `list-test-plan-testcase-comments` | Get test plan case comment list | --id --testplan-id |
| `get-test-plan-progress-rate` | Get test plan case execution progress statistics | --test-plan-identifier |
| `get-test-plan-result-directory-list` | Get test plan result directory list | --test-plan-identifier |
| `create-test-plan` | Create test plan | --manager-identifiers --name |
| `create-test-plan-testcase-comment` | Create test plan case comment | --content --id --testplan-id |
| `update-test-plan` | Update test plan | --test-plan-identifier |
| `add-test-plan-testcases` | Add test cases to a test plan | --testcase-ids --testplan-id |

## 用例与目录（13 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-directories` | Get test case directory list | --id |
| `list-test-repo` | Get test repository list | — |
| `list-test-repo-tags` | Get test repository label list | --id |
| `list-testcase-comments` | Get test case comment list | --id --test-repo-id |
| `get-testcase` | Get test case information | --id --test-repo-id |
| `get-testcase-field-config` | Get test case field configuration | --id |
| `search-testcases` | Search test cases | --id |
| `create-directory` | Create test case directory | --id --name |
| `create-test-repo` | Create test repository | --name |
| `create-testcase` | Create test case | --id |
| `create-testcase-comment` | Create test case comment | --content --id --test-repo-id |
| `update-testcase` | Update test case information | --id --test-repo-id |
| `delete-testcase` | Delete test case | --id --test-repo-id |

## 测试结果（5 条）

| 命令 | 用途 | 必填参数 |
| --- | --- | --- |
| `list-test-result-bug-relations` | Query bug list associated with the test result | --id --testplan-id |
| `get-test-result-list` | Get test case list in test plan | --directory-identifier --test-plan-identifier |
| `create-test-result-bug-relation` | Bugs linked to the test result | --id --testplan-id --workitem-id |
| `update-test-result` | Update test result | --id --testplan-id |
| `delete-test-result-bug-relation` | Unlink the bug associated with a test result | --id --testplan-id --workitem-id |
