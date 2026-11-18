# CachyOS 基础配置

<div align="center">

![CachyOS](https://img.shields.io/badge/OS-CachyOS-1793D1?logo=arch-linux&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

**CachyOS 装机后的基础配置指南**

</div>

---

## 📖 项目介绍

CachyOS 是一个基于 Arch Linux、面向性能优化的发行版，开箱即用，但国内用户装完系统通常还要完成几件事：

- 了解 **包管理器**（装一切软件的前提）
- 配置 **中文输入法**
- 修复 **WPS 中文输入** 这个经典坑
- 安装 **常用软件**

本项目把以上内容的用法整理成一套文档和脚本，方便自己复用，也欢迎参考。

## 📚 文档导航

| 文档 | 内容 |
| ---- | ---- |
| [docs/package-manager](package-manager.md) | CachyOS 几种包管理器介绍（pacman / paru / flatpak） |
| [docs/input-method](Chinese-input-method.md) | 中文输入法安装与配置（Fcitx5） |
| [docs/wps-fcitx-fix.md](wps-fcitx-fix.md) | WPS 无法输入中文的问题修复 |
| [docs/AppsInstallList.md](AppsInstallList.md) | 常用软件安装清单 |

**推荐按顺序阅读**：包管理器 → 输入法 → WPS 修复 → 常用软件。

## 📂 目录结构

```
CachyOS-Setup-Guide/
├── README.md                  # 主页（本文件）
├── docs/
│   ├── pacman.md              # 包管理器介绍
│   ├── input-method.md        # 中文输入法安装
│   ├── wps-fix.md             # WPS 输入法修复
│   └── apps.md                # 常用软件安装清单
└── LICENSE


```

## 📜 License

[MIT License](LICENSE)

