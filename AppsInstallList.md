# CachyOS 常用软件安装清单

> 适用于 CachyOS（基于 Arch Linux）。更新日期：2026-11-18

---

## 0. 准备工作

### 0.1 更新系统

```bash
sudo pacman -Syu
```

### 0.2 确认 AUR 助手

CachyOS 默认已安装 `paru`，以下命令中 `yay` 均可替换为 `paru`。如需 yay：

```bash
sudo pacman -S yay
```

---

## 1. 常用软件清单

### 1.1 Google Chrome

谷歌浏览器。

```bash
yay -S google-chrome
```

- 项目地址：https://www.google.com/chrome/
- AUR 页面：https://aur.archlinux.org/packages/google-chrome

### 1.2 LocalSend

开源的局域网文件互传工具（类似 AirDrop）。

```bash
# 方式一：Flatpak（推荐）
flatpak install flathub org.localsend.localsend_app

# 方式二：AUR
yay -S localsend-bin
```

- 项目地址：https://localsend.org
- GitHub：https://github.com/localsend/localsend

### 1.3 WPS Office

办公软件（Linux 中文社区版）。

```bash
yay -S wps-office-cn ttf-wps-fonts
```

- `wps-office-cn`：中文官方版
- `ttf-wps-fonts`：WPS 专用字体（不装会字体错乱）

- 项目地址：https://linux.wps.cn/
- AUR 页面：https://aur.archlinux.org/packages/wps-office-cn

### 1.4 Sidra

优雅的 Apple Music 桌面客户端，音频原样直通，Linux 下支持 MPRIS（媒体键/系统音量/通知）。

```bash
yay -S sidra-bin
```

- 项目地址：https://github.com/wimpysworld/sidra
- AUR 页面：https://aur.archlinux.org/packages/sidra-bin

### 1.5 LX Music（洛雪音乐）

基于 Electron + Vue 的免费音乐软件。

```bash
yay -S lx-music-desktop-bin
```

- 项目地址：https://github.com/lyswhut/lx-music-desktop
- 官网：https://lxmusic.toside.cn
- AUR 页面：https://aur.archlinux.org/packages/lx-music-desktop-bin

### 1.6 Bilibili（哔哩哔哩）

**方式一：官方客户端（AUR，转制官方 rpm/deb）**

```bash
yay -S bilibili-bin
```

**方式二：AppImage 版**

```bash
yay -S bilibili-appimage
```

**方式三：wiliwili（开源第三方客户端，Flatpak，体验接近官方 PC 客户端，支持手柄）**

```bash
flatpak install flathub cn.xfangfang.wiliwili
```

- 官方客户端：https://app.bilibili.com/
- wiliwili：https://github.com/xfangfang/wiliwili

---

## 2. Flatpak 使用与中科大镜像加速

### 2.1 安装并启用 Flathub

```bash
sudo pacman -S flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### 2.2 Flathub 更换中科大镜像（下载提速关键）

```bash
sudo flatpak remote-modify flathub --url=https://mirrors.ustc.edu.cn/flathub
```

验证是否生效：

```bash
flatpak remotes --show-details
# Url 应显示为 https://mirrors.ustc.edu.cn/flathub
```

### 2.3 常用 Flatpak 命令

```bash
flatpak search 关键词          # 搜索应用
flatpak install flathub 应用ID  # 安装应用
flatpak update                 # 更新全部 Flatpak 应用
flatpak uninstall 应用ID       # 卸载应用
flatpak list                   # 查看已安装应用
```

---

## 3. Gear Lever（AppImage 管理器）

专为 AppImage 设计的管理工具：把 `.AppImage` 文件拖进窗口即可自动安装、更新并集成到应用菜单，不用再手动 `chmod +x` 和创建桌面文件。

### 3.1 安装

```bash
flatpak install flathub it.mijorus.gearlever
```

### 3.2 使用

1. 从网上下载任意 `.AppImage` 文件（如 Sidra、LX Music 的 AppImage）
2. 打开 Gear Lever，将文件拖入窗口
3. 点击「Integrate and run」，自动出现在应用菜单中，可自动检查更新

- 项目地址：https://github.com/mijorus/gearlever
- Flathub：https://flathub.org/apps/it.mijorus.gearlever

---

## 4. 附加推荐软件

| 软件 | 安装命令 | 项目地址 |
|------|----------|----------|
| VS Code | `yay -S visual-studio-code-bin` | https://code.visualstudio.com/ |
| QQ | `flatpak install flathub com.qq.QQ` | https://im.qq.com/linuxqq/ |
| 微信 WeChat | `flatpak install flathub com.tencent.WeChat` | https://linux.weixin.qq.com/ |
| Obsidian | `yay -S obsidian-bin` | https://obsidian.md/ |
| VLC 播放器 | `sudo pacman -S vlc` | https://www.videolan.org/ |
| Steam | `sudo pacman -S steam` | https://store.steampowered.com/ |
| Flameshot 截图 | `sudo pacman -S flameshot` | https://flameshot.org/ |
| 输入法 Fcitx5 | `sudo pacman -S fcitx5 fcitx5-configtool fcitx5-qt fcitx5-gtk fcitx5-chinese-addons` | https://fcitx-im.org/ |

---

## 5. 常见问题及解决方法

### Q1：`yay/paru` 提示 `target not found`

先刷新数据库再安装：

```bash
sudo pacman -Syyu
```

### Q2：AUR 构建时报错缺少编译工具

```bash
sudo pacman -S --needed base-devel git
```

### Q3：安装时 GPG 签名验证失败

更新密钥环：

```bash
sudo pacman -S archlinux-keyring cachyos-keyring
```

### Q4：`curl: (60) SSL certificate problem` 或镜像同步失败

通常是系统时间不对导致证书校验失败：

```bash
timedatectl set-ntp true
```

### Q5：Flatpak 下载速度依然很慢

确认镜像已切换（见 2.2），并尝试更新应用源元数据：

```bash
sudo flatpak update --appstream
```

### Q6：WPS 打开后字体全是方框/乱码

缺少 WPS 专用字体：

```bash
yay -S ttf-wps-fonts
```

### Q7：WPS 无法打开 PDF

安装独立 PDF 阅读器替代，或安装 WPS PDF 组件：

```bash
sudo pacman -S okular   # KDE 环境
sudo pacman -S evince   # GNOME 环境
```

### Q8：LX Music 无法播放，提示接口/网络错误

属于音源接口问题：进入「设置 → 自定义源管理」，导入可用的自定义音源（网络上搜索"sixyin 音源"等）；若 GitHub 访问慢，可使用镜像站下载音源与软件。

### Q9：AppImage 双击无反应

缺少 FUSE2 运行库：

```bash
sudo pacman -S fuse2
```

临时方案：右键以 `--appimage-extract-and-run` 参数运行。根治方案：安装 Gear Lever 统一管理（见第 3 节）。

### Q10：系统大版本更新后，AUR 软件打不开（如 libicu 报错）

依赖库升级导致，重新编译安装该软件即可：

```bash
yay -S 软件名
```

### Q11：Chrome 在 Wayland 下卡顿/模糊

```bash
# 在 chrome://flags 中搜索 ozone，选择 Wayland
# 或启动参数：
google-chrome-stable --ozone-platform=wayland
```

### Q12：B 站客户端播放黑屏/花屏

客户端设置中关闭「硬件加速」，或切换解码方式为 AVC（H.264）。

### Q13：Chrome/VS Code 里 Fcitx5 输入法无法使用

添加环境变量（写入 `~/.bashrc` 或 `~/.config/fish/config.fish` 等对应 shell 配置）：

```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```

然后重启或重新登录。

---

> 💡 提示：AUR 软件建议定期 `yay -Syu` 全量更新，避免官方仓库大版本滚动后 AUR 软件因依赖问题无法启动。
