---
name: setup
description: Check or install WizTree, confirm the executable and version, establish Administrator-mode scanning, and verify with one small CSV export before any disk analysis.
---

# 设置 WizTree

目标是让 WizTree 可用且扫描结果可信。已有安装直接复用；不因为再次运行 setup 就重装、升级或改配置。

## 0. 四层状态，逐层判断

**已安装 ≠ 能导出 ≠ 有管理员权限 ≠ 覆盖全部文件。** 按「安装 → 权限 → 验证」的顺序确认，缺哪层就停在哪层并明确报告，不要把上层的成功当成本层通过。

## 1. 检查安装

```bash
ls -l "/c/Program Files/WizTree/WizTree64.exe"
```

```powershell
(Get-Item "C:\Program Files\WizTree\WizTree64.exe").VersionInfo.FileVersion
```

- 能打印版本（本机实测 `4.33`）→ 进第 2 步。
- 不在默认路径 → 先找便携版：`command -v WizTree64.exe`，或让用户给出解压目录。找到后用完整路径，不要求安装。
- 都没有 → 进第 2 步安装。

## 2. 安装

1. **winget**（本机实测可用）：

   ```powershell
   winget install --id AntibodySoftware.WizTree -e --accept-source-agreements --accept-package-agreements
   ```

2. **官方安装包**：<https://diskanalyzer.com/download>，文件名为 `wiztree_<major>_<minor>_setup.exe`（4.33 对应 `wiztree_4_33_setup.exe`）。版本号会变，以页面为准；不要用第三方下载站。
3. **便携版**：同页面的 `wiztree_<major>_<minor>_portable.zip`，解压到可写目录直接运行，不写注册表。没有管理员权限或不想安装时用这个。

安装或解压会改动本机，先说明来源和目标目录。

## 3. 权限

官方说明：WizTree 需要 **Administrator** 权限才能高速读取 NTFS 的 MFT，并访问全部系统文件。

- GUI 点「以管理员身份重新启动」；命令行加 `/admin=1`（可能弹 UAC）。
- 非管理员仍可扫描，但 `C:\Windows`、其他用户目录、`System Volume Information` 可能缺失，大小偏小；报告里必须注明权限和扫描范围。
- MFT 只在**整块 NTFS 盘**作为目标时启用；扫单个目录走普通遍历，慢一些但可用。非 NTFS、网络盘、U 盘同理。

## 4. 许可证

WizTree 对**个人使用免费**，商业环境需要购买 license；它是闭源软件，不是开源工具。本插件只是 Skill 封装，不内置、不重新分发二进制。上游软件和商标归 Antibody Software 所有。

## 5. 只读验证

```bash
"/c/Program Files/WizTree/WizTree64.exe" 'C:\Windows\System32\drivers' \
  '/export=D:\wiztree_setup_test.csv' /exportmaxdepth=1 /exportfiles=1
```

```powershell
& "C:\Program Files\WizTree\WizTree64.exe" "C:\Windows\System32\drivers" /export="D:\wiztree_setup_test.csv" /exportmaxdepth=1 /exportfiles=1
```

成功标准：退出码 0，且 CSV 存在、第二行是表头（中文 Windows 为 `文件名称,大小,分配,修改时间,属性,文件,文件夹`，英文为 `File Name,Size,Allocated,Modified,Attributes,Files,Folders`）。

> 陷阱：扫描目标和 `/export` 路径**必须用反斜杠**。本机 4.33 实测，写成 `D:/scan/x.csv` 时退出码仍是 0，但不生成文件。Git Bash 里用单引号包住路径，避免反斜杠被转义。

这一步只证明「能扫描并导出」，**不证明**结果覆盖了所有系统目录——那要管理员整盘扫描后才知道。

## 完成报告

报告：可执行文件路径与版本、安装方式（winget / 安装包 / 便携版）、是否具备管理员权限、验证 CSV 的路径与表头。缺失项写清是哪一层：没装 / 不能导出 / 没管理员权限 / 结果不完整，并给出对应的下一步命令。

## 来源

- [WizTree 官网](https://diskanalyzer.com/)
- [下载页](https://diskanalyzer.com/download)
- [命令行指南](https://diskanalyzer.com/guide)
