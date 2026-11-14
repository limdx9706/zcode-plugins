# WizTree 命令行参数

来源：官方指南 <https://diskanalyzer.com/guide> 与 4.33 更新日志，按本机 4.33 实测校正。

## 基本语法

```text
wiztree64.exe "<盘符或目录>" /export="<CSV 路径>" [参数...]
```

- 扫描目标是盘符（`C:\`）或目录（`C:\Windows`），**路径用反斜杠**。
- 命令同步执行，进程退出即导出完成；批处理里用 `start /wait`。
- 32 位 `wiztree.exe` 在 64 位系统上自动转到 64 位并保持参数不变。

## 导出参数

| 参数 | 取值 | 作用 |
| --- | --- | --- |
| `/export="文件名"` | 路径 | 导出 CSV。`%d` = 日期 `YYYYMMDD`，`%t` = 时间 `HHMMSS`；`.bat` 中写 `%%d`/`%%t` |
| `/exportfiles=0\|1` | 默认 1 | 0 = 不导出文件行 |
| `/exportfolders=0\|1` | 默认 1 | 0 = 不导出目录行；文件与目录至少保留一类，否则导出无效 |
| `/filter="规则"` | 文件通配 | 只导出匹配项，对完整路径和文件名匹配；多扩展名用 `\|` 连接，如 `"*.mp3\|*.wav\|*.ogg"` |
| `/filterexclude="规则"` | 文件通配 | 排除匹配项，格式同 `/filter` |
| `/filterfullpath=0\|1` | 默认 1 | 1 = 匹配完整路径；0 = 只匹配文件名（4.13+） |
| `/admin=0\|1` | 默认 0 | 1 = 管理员模式（MFT 高速扫描，可能弹 UAC） |
| `/sortby=0\|1\|2\|3` | 默认 0 | 0 = 名称；1 = 大小降序；2 = 分配大小降序；3 = 修改时间降序（4.13+） |
| `/exportmaxdepth=n` | 0 = 不限 | 限制目录深度 |
| `/exportdrivecapacity=0\|1` | 默认 1 | 根行追加 `DRIVECAPACITY,FREESPACE,USEDSPACE,RESERVEDSPACE` |
| `/exportUTCTime=0\|1` | 默认 0 | 1 = 时间用 UTC |
| `/exportalldates=1` | 开关 | 追加「最后访问」「创建」时间列 |
| `/exportallsizes=1` | 开关 | 追加目录自身合计（不含子目录）的总大小与分配大小 |
| `/exportsplitfilename=1` | 开关 | 拆成根、目录、文件名、扩展名四列 |
| `/exportmftrecno=0\|1` | 默认 0 | 追加 MFT 记录号；仅管理员整盘扫描时有效 |
| `/exportpercentofparent=1` | 开关 | 官方指南说明为追加「占父级百分比」；4.33 实测默认表头未出现，使用前先小样本验证 |
| `/exportfiletypes="文件名"` | 路径 | 另导出一份文件类型统计 CSV，可与 `/export` 同次扫描并用 |
| `/sortbyfiletypes=0\|1\|2` | 默认 1 | 文件类型统计排序：0 名称；1 大小降序；2 分配大小降序 |
| `/dumpmftfile="文件名"` | 路径 | 导出 MFT 文件（需管理员）；支持 `%d`/`%t` |

## 树图参数

| 参数 | 取值 | 作用 |
| --- | --- | --- |
| `/treemapimagefile="文件名"` | PNG 路径 | 导出 treemap PNG；支持 `%d`/`%t` |
| `/treemapimagewidth=n` | 默认 1920 | 图片宽度 |
| `/treemapimageheight=n` | 默认 1080 | 图片高度 |
| `/treemapimagegray=0\|1` | 默认 0 | 1 = 灰度 |
| `/treemapimagefreespace=0\|1` | 默认 1 | 是否显示可用空间 |
| `/treemapimageshowallocated=0\|1` | 默认 0 | 1 = 按分配大小而不是逻辑大小着色 |

## 其他参数

更新日志/安装器提到、官方指南表格未列出的项：

| 参数 | 作用 |
| --- | --- |
| `/configfolder="目录"` | 指定配置目录（便携 / 多用户场景） |
| `/supportercode=...` | 安装器参数：写入支持者代码 |
| `/runasadmin=TRUE\|FALSE` | 安装器参数：默认是否以管理员运行 |
| `/checkforupdates=TRUE\|FALSE` | 安装器参数：是否自动检查更新 |
| `/debug=1` | 生成调试日志 |

## 过滤器引号

从 4.11 起，`/filter`、`/filterexclude` 支持用**单引号表示字面双引号**；连续两个单引号表示一个字面单引号。例如只保留两个 Program Files 目录：

```text
/filter="'C:\Program Files\'|'C:\Program Files (x86)\'"
```

## CSV 格式

默认表头：`File Name,Size,Allocated,Modified,Attributes,Files,Folders`（中文 Windows 为 `文件名称,大小,分配,修改时间,属性,文件,文件夹`）。第一行是生成信息，表头在第二行。

- **目录行**：名称以 `\` 结尾；`Size`/`Allocated` 已包含全部子孙，跨行相加会重复计算。
- **文件行**：`Size`/`Allocated` 只代表该文件。
- **Allocated**：磁盘实际占用；值前导 `0` 表示硬链接，不额外占盘。
- **Modified**：`yyyy/mm/dd HH:mm:ss`。
- **Attributes**：位标志之和：1 只读、2 隐藏、4 系统、32 归档、2048 压缩（如只读 + 隐藏 = 3）。
- **Files / Folders**：目录行下面的文件数 / 子目录数。
- 根行额外带 `DRIVECAPACITY`、`FREESPACE`、`USEDSPACE`、`RESERVEDSPACE`。
- 可选列由 `/exportalldates`、`/exportallsizes`、`/exportsplitfilename`、`/exportmftrecno` 追加。

## 示例

整盘 CSV + 树图（管理员）：

```powershell
& "C:\Program Files\WizTree\WizTree64.exe" "C:\" `
  /export="D:\scan\c_%d_%t.csv" `
  /treemapimagefile="D:\scan\c_%d_%t.png" `
  /treemapimagewidth=1024 /treemapimageheight=768 `
  /treemapimagefreespace=0 /treemapimageshowallocated=1 `
  /exportalldates=1
```

按扩展名过滤、只看文件名匹配：

```powershell
& "C:\Program Files\WizTree\WizTree64.exe" "D:\" /export="D:\scan\media.csv" /filter="*.mp4|*.mkv|*.mov" /filterfullpath=0
```

只导目录、两层、按大小排序：

```powershell
& "C:\Program Files\WizTree\WizTree64.exe" "C:\Users" /export="D:\scan\users_folders.csv" /exportfiles=0 /exportmaxdepth=2 /sortby=1
```

## 来源

- [WizTree 命令行指南](https://diskanalyzer.com/guide)
- [WizTree 官网](https://diskanalyzer.com/)
