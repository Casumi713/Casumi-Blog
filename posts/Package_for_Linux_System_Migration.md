---
title: Linux系统迁移的包管理准备
published: 2026-07-09
pinned: true
description: 我即将进行重装系统，为此我正在进行准备。
tags:
  - Thoughts_Ideas
  - Blogging
category: Linux
licenseName: Unlicensed
author: casuki
draft: false
---
这是一个经过深度清洗、剔除所有依赖库（`-libs`, `-common`, `-data`）、字体包、语言包和底层组件后的**纯用户级应用与工具清单**。

这份列表只保留了你**主动安装**或**显式需要**的软件，重装时直接复制使用即可：

### 🖥️ 桌面环境与窗口管理
```bash
# KDE Plasma 核心 (按需选择，若以Hyprland为主可仅装基础包)
plasma-desktop plasma-workspace sddm-wayland-plasma kde-cli-tools
# Hyprland 生态
hyprland hyprlock hyprpaper hypridle niri wofi grimblast slurp wl-clipboard
# 主题与美化
papirus-icon-theme breeze-gtk qt6ct adwaita-qt6
```

### 💻 终端与 Shell
```bash
kitty ghostty zsh tmux starship fastfetch btop htop bat eza fd fzf ripgrep
```

### 🛠️ 开发与容器 (推荐将具体语言放入 Distrobox)
```bash
# 容器化
podman podman-compose distrobox toolbox buildah skopeo
# 编辑器 / IDE
neovim vim vscode codium kate
# 版本控制与工具
git git-lfs gh lazygit delta
# 语言运行时 (仅保留系统级必需的，其余建议进容器)
rust cargo golang nodejs python3 zig clang gcc make cmake meson ninja-build
# AI / GPU 计算 (⚠️ 仅 NVIDIA 显卡保留 cuda，AMD 保留 rocm)
akmod-nvidia xorg-x11-drv-nvidia-cuda nvidia-persistenced
# rocm-hip rocm-smi  # ← AMD 显卡取消注释此行
```

### 🎨 多媒体与创作
```bash
ffmpeg vlc mpv krita okular foliate calibre spectacle pipewire wireplumber
```

### 📦 办公与文档
```bash
libreoffice thunderbird kmail ark p7zip unzip zip
```

### 🌐 网络与安全
```bash
firefox chromium networkmanager-openvpn networkmanager-wireguard wireshark keepassxc firewalld
```

### 🔧 系统工具
```bash
dnf5 dnf5-plugins rpmfusion-free-release rpmfusion-nonfree-release
fedora-third-party flatpak fwupd tlp powertop smartmontools
```

---

### ⚠️ 重装执行注意事项

1.  **不要直接粘贴全部命令**：先确认你的主力桌面是 KDE 还是 Hyprland，二选一作为核心，另一个仅保留最小可用包。
2.  **NVIDIA 驱动必须单独处理**：
    ```bash
    # 启用 RPM Fusion 后再装驱动
    sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda
    # 重启后验证: nvidia-smi
    ```
3.  **字体无需手动安装**：上述应用会自动拉取所需字体。如果你有特殊字体需求（如编程字体），仅需额外安装 `jetbrains-mono-fonts` 或 `fira-code-fonts` 等 1-2 个包。
4.  **开发环境强烈建议容器化**：
    ```bash
    # 示例：创建 Rust 开发容器，而非在宿主机装 rust
    distrobox create --name rust-dev --image fedora:42
    distrobox enter rust-dev
    sudo dnf install rust cargo
    ```
    这样宿主机始终保持干净，重装恢复只需几条 `distrobox create` 命令。
5.  **配置比包更重要**：重装前务必备份 `~/.config`、`~/.local/share/nvim`、`~/.zshrc`、SSH/GPG 密钥。包可以随时装，配置丢了才是真痛苦。


---


# flatpak

```
WPS 365	cn.wps.wps_365	12.1.2.24722	stable	flathub	user
Saber	com.adilhanney.saber	1.34.3	stable	flathub	user
百度网盘	com.baidu.NetDisk	4.17.8	stable	flathub	user
Rnote	com.github.flxzt.rnote	0.14.2	stable	flathub	user
Flatseal	com.github.tchx84.Flatseal	2.4.0	stable	flathub	user
Xournal++	com.github.xournalpp.xournalpp	1.3.3	stable	flathub	user
Inochi Creator	com.inochi2d.inochi-creator	0.8.6	stable	flathub	user
Inochi Session	com.inochi2d.inochi-session	0.8.7	stable	flathub	user
OBS Studio	com.obsproject.Studio	32.1.0	stable	flathub	user
Pixelorama	com.orama_interactive.Pixelorama	1.1.9	stable	flathub	user
app	com.sigmafilemanager.app		master	app-origin	user
Wemeet	com.tencent.wemeet	3.26.10.401	master	wemeet-origin	user
ProtonPlus	com.vysp3r.ProtonPlus	0.5.17	stable	flathub	system
Reaper	fm.reaper.Reaper	7.66	stable	flathub	user
KikoPlay	io.github.KikoPlayProject.KikoPlay	2.0.0	stable	flathub	user
Weylus Community Edition	io.github.electronstudio.WeylusCommunityEdition	2024.8.1	stable	flathub	user
Aerion	io.github.hkdb.Aerion	0.2.5	stable	flathub	user
Hidamari	io.github.jeffshee.Hidamari	3.6	stable	flathub	user
LMMS	io.lmms.LMMS	1.2.2	stable	flathub	user
任务中心	io.missioncenter.MissionCenter	1.1.0	stable	flathub	system
Typora	io.typora.Typora	1.12.4	stable	flathub	system
Anki	net.ankiweb.Anki	25.09.02	stable	flathub	user
Lutris	net.lutris.Lutris	0.5.22	stable	flathub	system
FamiStudio	org.famistudio.FamiStudio	4.5.0	stable	flathub	user
TAP-plugins	org.freedesktop.LinuxAudio.Plugins.TAP	1.0.1	25.08	flathub	user
SWH	org.freedesktop.LinuxAudio.Plugins.swh	0.4.17	25.08	flathub	user
Freedesktop Platform	org.freedesktop.Platform	freedesktop-sdk-23.08.34	23.08	flathub	user
Freedesktop Platform	org.freedesktop.Platform	freedesktop-sdk-24.08.30	24.08	flathub	user
Freedesktop Platform	org.freedesktop.Platform	freedesktop-sdk-25.08.8	25.08	flathub	system
i386	org.freedesktop.Platform.Compat.i386		25.08	flathub	system
Mesa	org.freedesktop.Platform.GL.default	25.0.7	23.08	flathub	user
Mesa (Extra)	org.freedesktop.Platform.GL.default	25.0.7	23.08-extra	flathub	user
Mesa	org.freedesktop.Platform.GL.default	26.0.6	24.08	flathub	user
Mesa (Extra)	org.freedesktop.Platform.GL.default	26.0.6	24.08extra	flathub	user
Mesa	org.freedesktop.Platform.GL.default	25.3.5	25.08	flathub	system
Mesa	org.freedesktop.Platform.GL.default	26.0.8	25.08	flathub	user
Mesa (Extra)	org.freedesktop.Platform.GL.default	25.3.5	25.08-extra	flathub	system
Mesa (Extra)	org.freedesktop.Platform.GL.default	26.0.8	25.08-extra	flathub	user
nvidia-580-159-03	org.freedesktop.Platform.GL.nvidia-580-159-03		1.4	flathub	user
Mesa	org.freedesktop.Platform.GL32.default	25.3.5	25.08	flathub	system
Mesa (Extra)	org.freedesktop.Platform.GL32.default	25.3.5	25.08-extra	flathub	system
Nvidia VAAPI driver	org.freedesktop.Platform.VAAPI.nvidia		25.08	flathub	system
Nvidia VAAPI driver	org.freedesktop.Platform.VAAPI.nvidia		25.08	flathub	user
gamescope	org.freedesktop.Platform.VulkanLayer.gamescope	3.16.23	25.08	flathub	system
Codecs Extra Extension	org.freedesktop.Platform.codecs-extra		25.08-extra	flathub	system
Codecs Extra Extension	org.freedesktop.Platform.codecs-extra		25.08-extra	flathub	user
i386	org.freedesktop.Platform.codecs_extra.i386		25.08-extra	flathub	system
FFmpeg extension with extra codecs	org.freedesktop.Platform.ffmpeg-full		24.08	flathub	user
openh264	org.freedesktop.Platform.openh264	2.5.1	2.5.1	flathub	user
Freedesktop SDK	org.freedesktop.Sdk	freedesktop-sdk-25.08.11	25.08	flathub	user
GNOME Application Platform version 48	org.gnome.Platform		48	flathub	user
GNOME Application Platform version 49	org.gnome.Platform		49	flathub	system
GNOME Application Platform version 50	org.gnome.Platform		50	flathub	user
adw-gtk3 GTK Theme	org.gtk.Gtk3theme.adw-gtk3	6.5	3.22	flathub	user
Adwaita theme	org.kde.KStyle.Adwaita		5.15-25.08	flathub	user
Adwaita theme	org.kde.KStyle.Adwaita		6.10	flathub	user
KDE Application Platform	org.kde.Platform		5.15-25.08	flathub	user
KDE Application Platform	org.kde.Platform		6.10	flathub	user
KDE Application Platform	org.kde.Platform		6.8	flathub	user
Kdenlive	org.kde.kdenlive	26.04.0	stable	flathub	user
Krita	org.kde.krita	5.3.2.1	stable	flathub	user
Pix	org.kde.pix	4.0.1	stable	flathub	user
KeePassXC	org.keepassxc.KeePassXC	2.7.12	stable	flathub	user
qBittorrent	org.qbittorrent.qBittorrent	5.2.1	stable	flathub	user
gecko	org.winehq.Wine.gecko		stable-25.08	flathub	system
mono	org.winehq.Wine.mono		stable-25.08	flathub	system
osu!	sh.ppy.osu	2026.305.0-lazer	stable	flathub	user

```