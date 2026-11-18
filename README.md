<div align="center">

# CachyOS 基础配置

![OS](https://img.shields.io/badge/OS-CachyOS-1793D1?logo=arch-linux&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)
![PRs](https://img.shields.io/badge/PRs-welcome-brightgreen)
![Last Commit](https://img.shields.io/github/last-commit/JackXu06/CachyOS-Setup-Guide)

**CachyOS 装机后的基础配置指南**

</div>

---

> [!NOTE]
> **最后验证**：2026-11-18 · CachyOS rolling · KDE Plasma 6.8 · 内核 7.3.4
>
> CachyOS 是滚动发行版，本文内容可能随时间过时。执行任何命令前请先看清它在做什么，
> 重要数据记得备份；遇到更新后的异常，可以用 Limine 快照回滚。

## 📖 项目介绍

CachyOS 是一个基于 Arch Linux、面向性能优化的发行版，开箱即用，但国内用户装完系统通常还要完成几件事：

- 了解 **包管理器**（装一切软件的前提）
- 配置 **中文输入法**
- 修复 **WPS 中文输入** 这个经典坑
- 安装 **常用软件**

本项目把以上内容的用法整理成一套文档，方便自己复用，也欢迎参考和贡献。

## 🚀 快速开始

只想快速上手？按这个顺序走一遍：

1. **更新系统**

   ```bash
   sudo pacman -Syu
   ```

2. **配置中文输入法** → 详见 [中文输入法](Chinese-input-method.md)
3. **修复 WPS 输入中文** → 详见 [WPS 无法输入中文](wps-fcitx-fix.md)
4. **安装常用软件** → 详见 [常用软件清单](AppsInstallList.md)
5. **还不会用 pacman / paru？** → 先看 [包管理器介绍](package-manager.md)

## 📚 文档导航

| 文档 | 内容 |
| ---- | ---- |
| [包管理器介绍](package-manager.md) | pacman / paru / flatpak 的用法与区别 |
| [中文输入法](Chinese-input-method.md) | Fcitx5 安装与配置 |
| [WPS 输入修复](wps-fcitx-fix.md) | WPS 无法输入中文的问题修复 |
| [常用软件清单](AppsInstallList.md) | 装机后常用软件安装列表 |

**推荐阅读顺序**：包管理器 → 输入法 → WPS 修复 → 常用软件。

## 📂 目录结构

```text
CachyOS-Setup-Guide/
├── README.md                  # 主页（本文件）
├── LICENSE                    # MIT 协议
├── package-manager.md         # 包管理器介绍
├── Chinese-input-method.md    # 中文输入法安装
├── wps-fcitx-fix.md           # WPS 输入法修复
└── AppsInstallList.md         # 常用软件安装清单
```

> 如果你想改成 `docs/` 子目录结构：
>
> ```bash
> mkdir docs
> git mv package-manager.md Chinese-input-method.md wps-fcitx-fix.md AppsInstallList.md docs/
> ```
>
> 然后把上面所有链接加上 `docs/` 前缀即可。

## ❓ 常见问题

<details>
<summary>pacman、paru、flatpak 该用哪个？</summary>

- `pacman`：系统官方仓库和 AUR 之外的基础工具，最底层
- `paru`：AUR 助手，装仓库里没有的软件时用
- `flatpak`：沙盒化的桌面应用，和系统包互不干扰

具体命令见 [包管理器介绍](package-manager.md)。

</details>

<details>
<summary>WPS 还是打不出中文？</summary>

确认环境变量已包含 Fcitx5，然后**完全退出并重新登录**：

```bash
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
INPUT_METHOD=fcitx
```

详细步骤见 [WPS 输入修复](wps-fcitx-fix.md)。

</details>

<details>
<summary>更新后某个软件出错了怎么办？</summary>

先看它是不是这次更新引入的：

```bash
tail -100 /var/log/pacman.log
```

CachyOS 默认使用 Limine 引导并配了 Snapper 快照，可以在启动时选择之前的快照回滚。

</details>

<details>
<summary>装完软件没有桌面图标？</summary>

重新登录一次，或刷新桌面数据库：

```bash
update-desktop-database ~/.local/share/applications
```

</details>

## ⚠️ 免责声明

本文只是个人使用记录与经验整理，命令会改动系统配置。CachyOS 为滚动发行版，
软件版本和行为可能变化。请按自己的实际情况执行，作者不对任何数据丢失或系统问题负责。

## 🤝 贡献与反馈

发现内容过时、命令写错，或想补充新章节？欢迎：

- 提 [Issue](https://github.com/JackXu06/CachyOS-Setup-Guide/issues) 说明问题
- 直接发 Pull Request 修改文档

## 🗓️ 更新记录

- **2026-11-18**：补充 LICENSE；修正目录结构与链接；新增快速开始、FAQ、免责声明

## 📜 License

本项目采用 [MIT License](LICENSE)。你可以自由使用、修改、分发这些文档，但需保留版权声明。

---

<div align="center">

如果这份指南帮到了你，点个 ⭐ 就是最好的支持。

</div>

