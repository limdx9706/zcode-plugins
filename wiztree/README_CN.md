# WizTree 磁盘分析插件

[English](./README.md)

本插件是 WizTree（Windows 磁盘空间分析器）的轻量 Skill 封装：使用用户本机已安装的 WizTree，不内置二进制、不上传扫描数据，也不含 MCP 或远程服务。

封装由 lidongxin 维护，版本 0.1.0；这不是 Antibody Software 官方发布的插件。上游软件、文档和商标仍归 Antibody Software 所有。WizTree 是闭源软件，个人使用免费，商业环境需要授权。

## 快速 setup

运行 `/wiztree:setup`。先复用已安装的版本，只在缺失时处理：

```powershell
winget install --id AntibodySoftware.WizTree -e --accept-source-agreements --accept-package-agreements
# 或从 https://diskanalyzer.com/download 下载安装包 / 便携版 ZIP
```

WizTree 需要管理员权限才能高速读 MFT 并完整访问系统目录；普通权限也能扫描，但 `C:\Windows`、其他用户目录、`System Volume Information` 可能不完整。

## Skills

| Skill | 入口 | 用途 |
| --- | --- | --- |
| `setup` | `/wiztree:setup` | 检查/安装 WizTree、确认版本与管理员权限、跑一次只读 CSV 验证 |
| `cli` | 自动按上下文触发 | 扫描磁盘/目录、导出 CSV 与树图、读 CSV、树查看/文件查看/重复文件 |

## 覆盖范围

- **命令行**：整盘或目录扫描、CSV 导出（`/export`）、包含/排除过滤器、深度与排序、树图 PNG（`/treemapimagefile`）、文件类型统计、MFT 导出。
- **图形界面**：树查看 / 文件查看、treemap 下钻、按名称/大小/日期定位、重复文件面板、文件类型汇总、CSV/PNG 导出。
- **CSV**：本地化表头、目录行以 `\` 结尾、逻辑大小与分配大小、硬链接标记，以及可选的日期/大小/文件名/MFT 列。

## 安全边界

- WizTree 只做分析；删除和移动是独立动作，必须先展示路径、大小和影响并取得确认。
- 「删除 (到回收站)」不会立刻释放空间，要清空回收站后才会释放。
- 不要直接删 `WinSxS`、`pagefile.sys` 或 Windows Update 内部文件；用 DISM、`powercfg` 和先停服务的清理方式。
- 整盘 CSV 可能达到数百 MB，导出到有空间的盘。

## 上游文档

- [WizTree 官网](https://diskanalyzer.com/)
- [命令行指南](https://diskanalyzer.com/guide)
- [下载页](https://diskanalyzer.com/download)

启用或更新插件后请新建 ZCode session，确保 Skill 清单刷新。
