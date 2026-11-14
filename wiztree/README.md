# WizTree Disk Analysis Plugin

[中文文档](./README_CN.md)

This plugin is a lightweight Skill wrapper around WizTree, the Windows disk space analyzer: it uses the user's locally installed WizTree and does not bundle a binary, upload scan data, or ship an MCP server or remote service.

Wrapper maintained by lidongxin, version 0.1.0; this is not an Antibody Software-published plugin. Upstream software, documentation, and trademarks remain attributed to Antibody Software. WizTree is closed-source software, free for personal use; commercial use requires a license.

## Quick setup

Run `/wiztree:setup`. Reuse an installed copy first; only handle what is missing:

```powershell
winget install --id AntibodySoftware.WizTree -e --accept-source-agreements --accept-package-agreements
# or download the installer / portable ZIP from https://diskanalyzer.com/download
```

WizTree needs Administrator rights for fast MFT scanning and full access to system directories. Without them, scans still work, but `C:\Windows`, other users' profiles, and `System Volume Information` may be incomplete.

## Skills

| Skill | Entry | Purpose |
| --- | --- | --- |
| `setup` | `/wiztree:setup` | Check/install WizTree, confirm version and admin capability, run one read-only CSV verification |
| `cli` | Context-triggered | Scan drives/folders, export CSV and treemap PNG, read the CSV, browse Tree View / File View / duplicates |

## Coverage

- **CLI**: whole-drive or folder scans, CSV export (`/export`), include/exclude filters, depth and sort control, treemap PNG (`/treemapimagefile`), file-type summary, MFT dump.
- **GUI**: Tree View / File View, treemap drill-down, locate by name/size/date, Duplicate Files panel, file-type summary, CSV/PNG export.
- **CSV**: localized headers, folder rows ending in `\`, logical vs allocated size, hard-link marker, and optional date/size/filename/MFT columns.

## Safety boundary

- WizTree is an analysis tool. Deletions and moves are separate operations and need explicit confirmation with path, size, and impact.
- Deleting to the Recycle Bin from WizTree does **not** free disk space until the Recycle Bin is emptied.
- Do not delete `WinSxS`, `pagefile.sys`, or Windows Update internals directly; use DISM, `powercfg`, and service-aware cleanup instead.
- A full-drive CSV can reach hundreds of MB; export to a drive with free space.

## Upstream documentation

- [WizTree home](https://diskanalyzer.com/)
- [Command-line guide](https://diskanalyzer.com/guide)
- [Download page](https://diskanalyzer.com/download)

Open a new ZCode session after enabling or updating the plugin so the Skill catalog is refreshed.
