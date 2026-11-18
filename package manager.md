# CachyOS 包管理器详解

CachyOS 基于 Arch Linux，包管理是使用系统的核心技能。本文详细介绍三种最常用的软件安装方式：**pacman**（官方包管理器）、**paru**（AUR 助手）、**flatpak**（跨发行版沙盒包），以及它们各自的适用场景。

---

## 目录

- [一、为什么需要了解包管理器](#一为什么需要了解包管理器)
- [二、pacman：官方包管理器](#二pacman官方包管理器)
- [三、paru / yay：AUR 助手](#三paru--yayaur-助手)
- [四、Flatpak：跨发行版沙盒包](#四flatpak跨发行版沙盒包)
- [五、三种方式对比与选择](#五三种方式对比与选择)
- [六、常见问题](#六常见问题)

---

## 一、为什么需要了解包管理器

Linux 安装软件不同于 Windows 的"下载 exe 双击安装"，而是统一通过**包管理器**从软件仓库获取：

- ✅ 自动处理依赖关系，不用手动找 DLL/运行库
- ✅ 一条命令更新所有软件
- ✅ 卸载干净，不留垃圾文件
- ✅ 软件来源统一，安全可追溯

CachyOS 上常用的三条路线：

```
┌─────────────────────────────────────────────┐
│  官方仓库（pacman）── 系统组件、主流软件      │
│  AUR（paru/yay）   ── 社区维护的海量软件      │
│  Flathub（flatpak）── 沙盒应用、部分专有软件  │
└─────────────────────────────────────────────┘
```

> **日常只需记住一个原则：能用 paru 就不用 flatpak。**

---

## 二、pacman：官方包管理器

pacman 是 Arch 系发行版的官方包管理器，随系统自带，管理所有官方仓库软件包。

### 2.1 基本命令

```bash
# ── 安装 ──
sudo pacman -S 软件名              # 安装单个软件
sudo pacman -S 包1 包2 包3         # 一次安装多个
sudo pacman -S --needed 软件名     # 已安装则跳过（脚本中推荐加）

# ── 更新 ──
sudo pacman -Syu                   # 同步仓库 + 升级系统（最常用！）
sudo pacman -Sy                    # 仅同步仓库（一般不单独用）

# ── 搜索与查询 ──
pacman -Ss 关键词                  # 在仓库中搜索
pacman -Qs 关键词                  # 查询已安装的软件
pacman -Si 软件名                  # 查看软件详细信息
pacman -Qi 软件名                  # 查看已安装软件的详细信息
pacman -Ql 软件名                  # 列出软件安装的所有文件
pacman -Qo 文件路径                # 查询某个文件属于哪个包

# ── 卸载 ──
sudo pacman -R 软件名              # 仅卸载软件本体
sudo pacman -Rs 软件名             # 同时卸载不再需要的依赖（推荐）
sudo pacman -Rns 软件名            # 连配置文件一起删除（彻底卸载）

# ── 缓存清理 ──
sudo pacman -Sc                    # 清理未安装的包缓存
sudo pacman -Scc                   # 清空全部缓存（释放空间）
```

### 2.2 参数速查表

| 参数 | 含义 |
| ---- | ---- |
| `-S` | Sync，安装/同步仓库 |
| `-R` | Remove，卸载 |
| `-Q` | Query，查询本地 |
| `-s` | search，搜索 |
| `-i` | info，信息 |
| `-l` | list，列出文件 |
| `y` | 刷新仓库数据 |
| `u` | 系统升级 |
| `s` | 级联删除依赖 |

### 2.3 常用组合命令

```bash
# 更新系统前先看有哪些更新
pacman -Qu

# 找出孤立依赖（不再被任何软件需要）并清理
sudo pacman -Rns $(pacman -Qtdq)

# 查看占用空间最大的 10 个已安装软件
expac -H M '%m\t%n' | sort -rh | head -10
```

### 2.4 镜像源

CachyOS 安装时通常已选好国内镜像源。如需调整：

```bash
# 编辑镜像源列表
sudo nano /etc/pacman.d/mirrorlist

# 或自动测速排序（需安装 reflector）
sudo pacman -S reflector
sudo reflector --country China --age 12 --sort rate --save /etc/pacman.d/mirrorlist
```

### 2.5 特点小结

| 优点 | 缺点 |
| ---- | ---- |
| 官方仓库，稳定可靠 | 软件数量少于 AUR |
| 速度快（CachyOS 优化了国内镜像） | 没有图形界面 |
| 系统更新的唯一入口 | 专有软件（微信、QQ 等）基本不在官方源 |

---

## 三、paru / yay：AUR 助手

AUR（Arch User Repository）是 Arch 社区维护的巨大软件仓库，几乎能找到任何软件。**paru** 和 **yay** 是 AUR 的两个主流命令行助手，用法几乎一致，二选一即可（推荐 paru，Rust 编写，活跃维护）。

### 3.1 安装 paru

CachyOS 仓库已内置 paru，优先尝试：

```bash
sudo pacman -S paru
```

如果仓库里没有（或想手动安装）：

```bash
sudo pacman -S --needed base-devel git
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

### 3.2 基本命令

paru 的语法与 pacman 完全兼容，且自动支持 AUR：

```bash
# ── 安装（官方源 + AUR 通吃）──
paru -S 软件名                     # 官方源有就走官方源，没有则走 AUR
paru -S aur/软件名                 # 强制从 AUR 安装

# ── 更新（系统 + AUR 软件一键全更新）──
paru -Syu

# ── 搜索 ──
paru -Ss 关键词                    # 搜索官方源 + AUR
paru 关键词                        # 交互式搜索，直接选数字安装（超好用！）

# ── 查询 ──
paru -Qs 关键词                    # 查询已安装
paru -Qi 软件名                    # 软件信息

# ── 卸载 ──
paru -Rs 软件名                    # 与 pacman 一致

# ── AUR 专属 ──
paru -Su                           # 仅更新 AUR 软件
paru -Sc                           # 清理 AUR 构建缓存
paru -Qm                           # 列出所有 AUR 安装的软件
```

### 3.3 使用要点

**① 搜索安装是日常最高频操作：**

```bash
paru wps          # 列出所有相关包，输入数字回车即安装
```

**② 编译耗时是正常现象。**

AUR 软件常常需要从源码编译，第一次安装耗时较长（几分钟到几十分钟），编译缓存保存在 `~/.cache/paru/`，更新时会复用。

**③ 安装前看一眼 PKGBUILD。**

paru 安装前会提示查看 `PKGBUILD`（构建脚本），建议扫一眼 `source=` 行的下载地址是否正常，避免安装恶意脚本。

**④ 进阶选项（编辑 `~/.config/paru/paru.conf`）：**

```ini
[options]
BottomUp             # 搜索结果倒序（小的编号在最下面，方便选择）
SudoLoop             # 长时间编译时保持 sudo 权限
NewsOnUpgrade        # 更新前显示 Arch 新闻（重大变更提醒）
```

### 3.4 特点小结

| 优点 | 缺点 |
| ---- | ---- |
| 软件数量最多（微信、QQ、WPS、网易云等全都有） | 部分软件需本地编译，耗时 |
| 命令与 pacman 一致，学习成本低 | 软件质量由社区维护，需稍加甄别 |
| 一条命令同时更新系统和所有 AUR 软件 | 偶尔遇到编译失败需要看报错 |

---

## 四、Flatpak：跨发行版沙盒包

Flatpak 是与发行版无关的沙盒包格式，软件运行在隔离环境中，不依赖系统库。

### 4.1 安装与配置

```bash
# 安装
sudo pacman -S flatpak

# 添加 Flathub 官方仓库
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### 4.2 基本命令

```bash
# 安装 / 更新 / 卸载
flatpak install flathub 软件名
flatpak update                    # 更新所有 flatpak 软件
flatpak uninstall 软件名
flatpak uninstall --unused        # 清理无用运行时

# 搜索 / 查询
flatpak search 关键词
flatpak list                      # 列出已安装的 flatpak
flatpak info 软件名               # 软件信息

# 权限管理（沙盒应用权限查看与修改）
flatpak info --show-permissions 软件名
flatpak override --user 软件名    # 修改权限（如允许访问某个目录）
```

### 4.3 什么时候用 Flatpak

- ✅ paru 上找不到的软件（如某些国外应用的新版本）
- ✅ 想要与系统隔离的沙盒运行环境
- ✅ 需要多版本并存（开发测试场景）

### 4.4 Flatpak 的坑（重要！）

**沙盒与系统集成差**：

- ❌ **输入法问题**：Flatpak 应用里中文输入法（Fcitx5/IBus）经常失效或候选框位置错乱
- ❌ **主题不一致**：应用外观与系统主题脱节（GTK/Qt 主题需要单独配置）
- ❌ **文件访问受限**：默认只能访问少数目录，传文件可能不方便
- ❌ **磁盘占用大**：每个应用打包自己的运行时，重复占用空间

> **结论：CachyOS 上优先使用 paru 安装原生包，Flatpak 只作为补充。**

---

## 五、三种方式对比与选择

| 维度 | pacman | paru / yay | flatpak |
| ---- | ---- | ---- | ---- |
| 软件来源 | Arch 官方仓库 | 官方源 + AUR 社区仓库 | Flathub 沙盒仓库 |
| 软件数量 | 约 1 万+ | 官方源 + AUR 8 万+ | 数千个应用 |
| 安装方式 | 二进制直接安装 | 二进制或本地编译 | 下载沙盒运行时 |
| 更新命令 | `pacman -Syu` | `paru -Syu`（一键全更新） | `flatpak update` |
| 中文输入法兼容 | ✅ 完美 | ✅ 完美 | ⚠️ 经常出问题 |
| 磁盘占用 | 小 | 小 | 大（自带运行时） |
| 适合场景 | 系统组件、官方源软件 | **日常装软件的主力** | 找不到的补充 |

### 选择决策图

```
要装软件？
   │
   ├─ 系统更新 / 官方源有的 → pacman -Syu / pacman -S
   │
   ├─ 日常软件（WPS、微信、浏览器……）→ paru -S（首选！）
   │
   └─ paru 找不到 / 需要沙盒隔离 → flatpak install flathub
```

---

## 六、常见问题

**Q: paru 和 pacman 是什么关系？**
A: paru 是 pacman 的"前端增强版"，底层仍调用 pacman 管理官方包，额外扩展了 AUR 支持。日常用 paru 一条命令就够了。

**Q: AUR 软件安全吗？**
A: AUR 包本质是构建脚本（PKGBUILD），安装前会展示给你看。建议看一眼源码地址，避免来源不明的包。热门软件（数万投票）基本可信。

**Q: 更新系统时提示某个包冲突怎么办？**
A: 先阅读报错信息，通常是官方仓库结构调整。一般按提示先卸载冲突包再更新即可，必要时搜索错误信息。

**Q: pacman 提示 "error: target not found"？**
A: 说明官方源没有这个软件，试试 `paru 关键词`，大概率在 AUR。

**Q: Flatpak 软件里打不了中文怎么办？**
A: 这是沙盒环境的通病。优先卸载改用 paru 原生版；如果必须用 Flatpak，可尝试配置 `--env=QT_IM_MODULE=fcitx` 等环境变量，但成功率不保证。

**Q: 磁盘空间不够了，怎么清理？**
A: 依次执行：
```bash
paru -Sc                          # 清理 pacman/paru 缓存
flatpak uninstall --unused        # 清理 flatpak 无用运行时
sudo pacman -Rns $(pacman -Qtdq)  # 清理孤立依赖（无输出则没有）
```

---

> 📌 **一句话总结：系统更新用 `paru -Syu`，装软件用 `paru 关键词`，Flatpak 能不用就不用。**
