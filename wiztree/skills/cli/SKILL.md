---
name: cli
description: Use WizTree for Windows disk space analysis — scan drives or folders, export CSV and treemap PNG, locate large files and duplicates, read the CSV correctly, and turn results into safe cleanup plans. Use when the task involves disk full, large files/folders, duplicate files, storage reports, or cleanup advice.
---

# 使用 WizTree

所有磁盘空间分析通过本机 WizTree 完成。若本回合没有可信的 setup 结果，先读并执行 `../setup/SKILL.md` 的检查。

## 调用契约（先记住这三条）

1. **导出是「启动 GUI 进程 + 写文件」**：命令同步执行，进程退出即导出完成；它**不把数据打印到 stdout**。判断成败看退出码和 CSV/PNG 是否生成，不要用输出内容判断。
2. **路径必须用 Windows 反斜杠**：扫描目标和 `/export` 都一样。本机 4.33 实测，写成 `D:/scan/x.csv` 时退出码仍是 0，但不生成文件。Git Bash 里用单引号包住路径。
3. **表头随系统语言本地化**：第一行是 `生成由 WizTree 4.33 ...` 信息行，第二行才是表头；中文 Windows 为 `文件名称,大小,分配,修改时间,属性,文件,文件夹`，英文为 `File Name,Size,Allocated,Modified,Attributes,Files,Folders`。解析前先定位表头，不要写死英文列名。

## 范围

本 skill 覆盖整盘/目录扫描、CSV 与树图导出、GUI 浏览、重复文件候选。不做文件恢复、不做分区操作；删除/移动属于清理动作，按「写操作边界」处理。

32 位的 `WizTree.exe` 在 64 位系统上会自动再拉起 `WizTree64.exe`，参数一致；优先直接调 `WizTree64.exe`。

## 确认扫描范围与权限

- 整盘 NTFS + 管理员 → MFT 高速扫描，结果最全。
- 目录 / 非 NTFS / 无管理员 → 普通遍历，慢一些，且可能漏掉系统目录；报告里注明范围与权限。
- 用户只说「C 盘满了」→ 默认整盘；明确说了某个目录 → 才缩小范围，不要擅自替用户改。
- 需要管理员时先说明会弹 UAC，不要静默失败后反复重试。

## 命令速查

| 目的 | 命令 | 性质 |
|---|---|---|
| 整盘扫描 + CSV | `WizTree64.exe 'C:\' '/export=D:\scan\c_%d_%t.csv'` | 只读 |
| 目录扫描、限深度 | `WizTree64.exe 'C:\Users' '/export=D:\scan\users.csv' /exportmaxdepth=2` | 只读 |
| 按扩展名过滤 | `... /filter="*.mp4\|*.mkv\|*.mov" /filterfullpath=0` | 只读 |
| 树图 PNG | `... /treemapimagefile='D:\scan\c.png' /treemapimagewidth=1920` | 只读 |
| 文件类型统计 | `... /exportfiletypes='D:\scan\types.csv'` | 只读 |
| MFT 导出 | `... /dumpmftfile='D:\scan\mft.bin'` | 只读（需管理员） |
| 交互下钻 | GUI 的树查看 / 文件查看 | 只读 |
| 重复文件候选 | GUI 的重复文件面板 | 只读；删除前用哈希复核 |

`%d`/`%t` 会替换成日期 `YYYYMMDD` / 时间 `HHMMSS`；写在 `.bat` 里要写成 `%%d`/`%%t`。完整参数见 [references/parameters.md](references/parameters.md)。

整盘示例（CSV + 树图，管理员）：

```bash
"/c/Program Files/WizTree/WizTree64.exe" 'C:\' \
  '/export=D:\scan\c_%d_%t.csv' \
  /exportfiles=1 /exportfolders=1 /exportmaxdepth=0 /sortby=1 \
  '/treemapimagefile=D:\scan\c_%d_%t.png' \
  /treemapimagewidth=1920 /treemapimageheight=1080 \
  /admin=1
```

## CSV 解读

- **目录行以 `\` 结尾**；目录的 `大小` 已包含全部子孙，**不能跨行相加**（会重复计算）。要汇总就看某一深度的直接子项，或只看叶子文件。
- `大小` 是逻辑大小，`分配` 是磁盘占用；差异大时注意稀疏文件、压缩和硬链接。`分配` 前导 `0` 表示硬链接，不额外占盘。
- `文件` / `文件夹` 两列只对目录行有意义，是目录下的文件数与子目录数。
- 根行会带 `DRIVECAPACITY,FREESPACE,USEDSPACE,RESERVEDSPACE`；可用空间以 `FREESPACE` 为准。
- 可选列由 `/exportalldates`、`/exportallsizes`、`/exportsplitfilename`、`/exportmftrecno` 追加。
- `/exportpercentofparent=1` 在官方指南中有说明，但 4.33 实测默认表头未出现该列；用时先小样本验证，不要假设一定有。

## 图形界面

启动 `WizTree64.exe`，选驱动器或文件夹后点「扫描」。

- **树查看（Tree View）**：左侧目录树 + 右侧 treemap，点色块下钻、滚轮缩放；`颜色` 可按文件类型/文件夹/修改时间着色。
- **文件查看（File View）**：可搜索列表，支持 `包含过滤器` / `排除过滤器`、按名称/大小/日期定位、文件类型汇总。
- **重复文件（Duplicate Files）**：按大小和名称找候选，显示 `重复计数` / `重复大小`；WizTree 不做内容哈希，删除前用 MD5/SHA 复核。
- **导出**：CSV、`导出文件类型到 CSV`、`将树图图像另存为 .png`。
- **MFT Dump**：管理员下可导出 MFT，适合离线分析。
- 中文界面可用：`选项 → 语言` 选简体中文；需要完整访问系统目录时点「以管理员身份重新启动」。

## 写操作边界

- 分析、导出、树图都是只读操作，直接做。
- 删除和移动是独立动作：先展示路径、大小、归属和影响，取得确认后一次只处理一类。
- WizTree 的「删除 (到回收站)」**不会立刻释放磁盘空间**；要释放得清空回收站或使用「删除 (无法撤消删除)」。回收站本身也要在分析结果里看一眼。
- 不要直接删 `WinSxS`、`pagefile.sys` 或 Windows Update 内部文件；组件清理用 `DISM /Online /Cleanup-Image /StartComponentCleanup`，更新缓存要先停 `wuauserv`/`bits`，休眠文件用 `powercfg /h off`。
- 整盘 CSV 可能达到数百 MB，导出到有空间的盘。
- WizTree 个人使用免费；商业环境先确认 license。

## 完成报告

报告：扫描目标与权限、耗时、CSV/PNG 路径、Top 占用目录与文件、结论与建议（区分可安全回收 / 需确认 / 不要动）。只给数字不给路径不算完成。

## 来源

- [WizTree 命令行指南](https://diskanalyzer.com/guide)
- [WizTree 官网](https://diskanalyzer.com/)
- [下载页](https://diskanalyzer.com/download)
