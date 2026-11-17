### 第一步：准备基础环境
1.更新系统并安装基础依赖
`sudo pacman -Syu --needed base-devel git`

2.从 AUR 编译安装 paru
`git clone https://aur.archlinux.org/paru.git`
`cd paru`
`makepkg -si`
### 第二步：安装 Fcitx5 与雾凇拼音
`paru -S fcitx5-im fcitx5-rime rime-ice-git`
###  第三步：清理旧配置（重要）
`mv ~/.local/share/fcitx5/rime ~/.local/share/fcitx5/rime.bak 2>/dev/null`
### 第四步：设置环境变量
创建或编辑配置文件：
`nano ~/.config/environment.d/im.conf`
写入下面这一行
`XMODIFIERS=@im=fcitx`
### 第五步：部署与启用雾凇拼音
重新登录后，Fcitx5 应该已经由 KWin 自动拉起了。
### 第六步：手动重启部署
`fcitx5-remote -r`
