# Arch Linux + KDE Plasma 环境下修复 WPS Office 的 Fcitx5 与字体问题

> 测试环境：Arch Linux、KDE Plasma（X11 / Wayland 均可）、fcitx5、WPS Office
> 目标：解决 WPS 中文输入、中文字体缺失两个常见问题

---

## 目录

- [一、WPS 无法输入中文（fcitx5 不生效）](#一wps-无法输入中文fcitx5-不生效)
- [二、WPS 提示缺少宋体/黑体等中文字体](#二wps-提示缺少宋体黑体等中文字体)
- [三、恢复/撤销已做的修改](#三恢复撤销已做的修改)
- [四、常用排查命令速查](#四常用排查命令速查)

---

## 一、WPS 无法输入中文（fcitx5 不生效）

### 1.1 原因

WPS Office for Linux **自带了一套 Qt5 运行库**，默认只带 ibus 的输入法插件，没有 fcitx5 的 Qt5 平台输入上下文插件，所以在 WPS 里打不出中文，即使 fcitx5 在其他应用里正常工作。

### 1.2 安装 fcitx5 的 Qt5 前端

```bash
sudo pacman -S fcitx5-qt
```

确认插件文件位置（Arch 新版路径在 `/usr/lib/qt/plugins/`，不是旧版的 `/usr/lib/qt5/plugins/`）：

```bash
pacman -Ql fcitx5-qt | grep platforminputcontexts
# 典型输出：
# fcitx5-qt /usr/lib/qt/plugins/platforminputcontexts/libfcitx5platforminputcontextplugin.so
# fcitx5-qt /usr/lib/qt6/plugins/platforminputcontexts/libfcitx5platforminputcontextplugin.so
```

### 1.3 把 Qt5 插件复制进 WPS 的插件目录（关键步骤）

WPS 自带 Qt，插件目录不在系统 Qt 的路径下，需要手动把插件拷进去。

先确认 WPS 的安装路径：

```bash
pacman -Ql wps-office | grep -m1 office6
# Arch 上通常是 /opt/kingsoft/wps-office/office6（也可能有 /usr/lib/office6 软链）
```

复制插件（**注意用 `qt` 目录下的 Qt5 版，不要用 `qt6` 目录下的**）：

```bash
sudo mkdir -p /opt/kingsoft/wps-office/office6/plugins/platforminputcontexts
sudo cp /usr/lib/qt/plugins/platforminputcontexts/libfcitx5platforminputcontextplugin.so \
        /opt/kingsoft/wps-office/office6/plugins/platforminputcontexts/
```

### 1.4 只给 WPS 设置输入法环境变量（不动全局）

> KDE Plasma（尤其 Wayland 会话）下，**全局只需要 `XMODIFIERS=@im=fcitx`**，不要全局设置 `QT_IM_MODULE`，否则会干扰 Plasma 自身的输入法集成。

正确做法是通过覆盖 `.desktop` 启动文件，只给 WPS 单独注入变量。

#### 步骤 1：拷贝 desktop 文件到用户目录

```bash
cp /usr/share/applications/wps-office*.desktop ~/.local/share/applications/
```

#### 步骤 2：在每个 Exec 前插入 env 前缀

```bash
sed -i '/^Exec=/ s|^Exec=|Exec=env QT_IM_MODULE=fcitx XMODIFIERS=@im=fcitx |' \
    ~/.local/share/applications/wps-office*.desktop
```

改完后 `Exec` 行类似这样（`%U`/`%F` 保留）：

```
Exec=env QT_IM_MODULE=fcitx XMODIFIERS=@im=fcitx /usr/bin/wps %U
```

#### 步骤 3：确认 + 刷新缓存 + 重启 WPS

```bash
grep '^Exec=' ~/.local/share/applications/wps-office*.desktop
kbuildsycoca6 2>/dev/null || kbuildsycoca5
pkill -f office6
```

### 1.5 备选方案：插件加载失败时走 XIM

如果 WPS 自带的 Qt 版本与系统插件 ABI 不匹配，`QT_IM_MODULE=fcitx` 会导致插件加载失败（可通过 `QT_DEBUG_PLUGINS=1 QT_IM_MODULE=fcitx /usr/bin/wps` 确认，日志里搜 `fcitx`）。此时直接用 XIM 协议，fcitx5 自带 XIM 支持，不需要插件：

把 `.desktop` 里的 `QT_IM_MODULE=fcitx` 改成：

```
QT_IM_MODULE=xim
```

缺点：预编辑体验较差，候选词窗口不跟随光标（见第三章）。

---

## 二、WPS 提示缺少宋体/黑体等中文字体

### 2.1 原因

宋体（SimSun）、黑体（SimHei）是微软/方正版权字体，开源发行版不能自带。WPS 打开含有这些字体的文档时会报错，排版也会错乱。

### 2.2 方案 A：从 AUR 装 Windows 中文字体（推荐）

AUR 上正确的包名是 `ttf-ms-win11-auto-zh_cn`（注意是**下划线** `zh_cn`，不是 `zh-cn`）：

```bash
paru -S ttf-ms-win11-auto-zh_cn
```

这个包会从微软官方 Windows 11 镜像流式提取简体中文字体（宋体、黑体、微软雅黑、楷体、仿宋等），安装过程会比普通包慢一些，耐心等待。

> 另一个相关包 `ttf-wps-fonts` 装的是 WPS 缺的**符号字体**（Wingdings、Symbol 等），不包含宋体黑体，不要搞混。如果文档还提示缺符号字体，可以一并安装：
> ```bash
> paru -S ttf-wps-fonts
> ```

### 2.3 方案 B：从 Windows 拷贝字体

如果不方便用 AUR，从任意一台 Windows 机器的 `C:\Windows\Fonts\` 拷贝：

- `simsun.ttc`（宋体）
- `simhei.ttf`（黑体）
- 可选：`simkai.ttf`（楷体）、`simfang.ttf`（仿宋）、`msyh.ttc`/`msyhbd.ttc`（微软雅黑）

放到用户字体目录（没有就自己建）：

```bash
mkdir -p ~/.local/share/fonts
cp simsun.ttc simhei.ttf ~/.local/share/fonts/
fc-cache -fv
```

### 2.4 验证字体安装

```bash
fc-list | grep -iE "simsun|simhei|宋体|黑体"
pkill -f office6
```

重启 WPS 后报错消失、排版恢复正常即可。

---

## 三、恢复/撤销已做的修改

| 修改项 | 撤销方法 |
|---|---|
| 覆盖的 `.desktop` 文件 | `rm ~/.local/share/applications/wps-office*.desktop` |
| 拷贝进 WPS 的插件 | `sudo rm /opt/kingsoft/wps-office/office6/plugins/platforminputcontexts/libfcitx5platforminputcontextplugin.so` |
| 安装的 AUR 字体包 | `paru -Rns ttf-ms-win11-auto-zh_cn ttf-wps-fonts` |
| 手动复制的字体 | `rm ~/.local/share/fonts/simsun.ttc ~/.local/share/fonts/simhei.ttf && fc-cache -fv` |
| 在终端误执行的 `env ... /usr/bin/wps` | 无需处理，只影响那次启动的进程 |

撤销 `.desktop` 覆盖后刷新一下 KDE 缓存：

```bash
kbuildsycoca6 2>/dev/null || kbuildsycoca5
```

---

## 四、常用排查命令速查

```bash
# 查 fcitx5-qt 插件安装位置
pacman -Ql fcitx5-qt | grep platforminputcontexts

# 全盘搜 fcitx5 插件
find /usr/lib -name "libfcitx5*inputcontext*"

# 验证字体是否安装
fc-list | grep -iE "simsun|simhei|宋体|黑体"

# 刷新字体缓存
fc-cache -fv

# 查看 WPS 启动文件当前 Exec
grep '^Exec=' ~/.local/share/applications/wps-office*.desktop

# 调试 WPS 插件加载情况
QT_DEBUG_PLUGINS=1 QT_IM_MODULE=fcitx /usr/bin/wps 2>&1 | grep -i fcitx

# 完全重启 WPS
pkill -f office6

# 重启 fcitx5
fcitx5 -rd

# 刷新 KDE 应用缓存
kbuildsycoca6 2>/dev/null || kbuildsycoca5
```

---

## 关键路径速记

| 项目 | 路径 |
|---|---|
| fcitx5 Qt5 插件（系统） | `/usr/lib/qt/plugins/platforminputcontexts/libfcitx5platforminputcontextplugin.so` |
| WPS 插件目录 | `/opt/kingsoft/wps-office/office6/plugins/platforminputcontexts/` |
| 用户字体目录 | `~/.local/share/fonts/` |
| 用户 desktop 覆盖目录 | `~/.local/share/applications/` |
