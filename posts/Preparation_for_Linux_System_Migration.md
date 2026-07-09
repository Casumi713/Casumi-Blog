---
title: Linux系统迁移的准备
published: 2026-07-03
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

# Celestia系

## Celesita-Shell (QML+Rust)

这是基于hyprland上的caelestia-shell的niri移植版本进行修改而来的

现在的问题是进行了一定的重构和适配但是还存在较多的问题，还有不少的功能尚未实现

对了，刚刚发现点击通知没办法跳转/弹出对应应用

## Celestia-Wallpaper (Rust)

这是基于mpvpaper进行rust重构得到的，目前已经支持了图片、视频、spine(仅3.8)，本来想要支持web但是发现如果移植的话过于庞大了，后续想要使用基于windowtolayer来把任何程序的界面放置到background层面来实现，不过至今没有开始正式移植

后续也计划补充关于spine4.2的支持以及多壁纸的切换


## Celestia-TabletDriver (Kotlin+Rust)

这是基于Weylus实现的平板转数位板，目前专门针对wayland+华为平板优化(因为我只有华为平板)，分为两个部分，一部分是在linux端进行被控，另一部分是在安卓端进行手写笔操作的。现在的问题是

## Celestia-PDF-Reader (Zig)

这是基于Fancy-Cat实现的
## Celestia-Ranobe (Rust)

这是基于Hoshi-Reader-Android进行Rust重构得到的

opencode试了两次，claude+deepseek试了一次，codex+gpt5.5试了两次，全部失败

该项目现已废弃

# 部分已知问题

如果在关机前使用的是省电模式，那么再次开机之后会从SDDM界面就开始显示过曝

如果进入了windows系统并且触发了休眠或者睡眠，都有可能在回到linux时触发脏硬盘挂载失败导致进入紧急模式，建议在紧急模式中使用 `journalctl -xb | grep -i "ntfs\|mount"` 来确认是哪张硬盘的问题，并回到windows系统在管理员模式下使用 `chkdsk  E:  /f`这种命令来清理脏标记。

由于之前使用了虚拟显示器，因此导致没办法在linux系统中实现切换gpu模式为仅核显，且导致NVIDIA显卡的功耗持续处于高位，不过后来发现即使切换到了核显，cpu功耗也比win系统下要高


# 关于备份

**proj还没备份完**

**两个document的后续需要二次备份**

**两个download没备份过**

**APP的application列表没备份过**

**distroEnvs的zsh相关可以备份一下**

**~/rpmbuild 建议打包一下**

**根目录尤其要注意备份 .config/ 和 .local/ 以及 .zshrc 、.zhistory、.gitconfig、等等等等**

特别是各种来源的软件包都需要进行准备一下

还有一些无法显式复制的配置

**celestia-pdf-reader和kitim在copr上面都有，kew在terra仓库上也有**

**几个浏览器也要备份**

**还有keepassxc的密码**

最后记得要推送casumi blog 到github


# 关于重装

基于xdg-ninja

export XDG_CONFIG_HOME="$HOME/.config"
export XDG_DATA_HOME="$HOME/.local/share"
export XDG_STATE_HOME="$HOME/.local/state"
export XDG_CACHE_HOME="$HOME/.cache"

```
# ===
# XDG Base Directory Specification - 基础变量（必须最先设置）
# ===
export XDG_CONFIG_HOME="${XDG_CONFIG_HOME:-$HOME/.config}"
export XDG_DATA_HOME="${XDG_DATA_HOME:-$HOME/.local/share}"
export XDG_STATE_HOME="${XDG_STATE_HOME:-$HOME/.local/state}"
export XDG_CACHE_HOME="${XDG_CACHE_HOME:-$HOME/.cache}"
# XDG_RUNTIME_DIR 通常由 systemd/logind 自动设置，此处不设默认值

# ===
# Zsh & Oh My Zsh
# ===
# ZDOTDIR 必须在 .zshenv 中设置，且先于 OMZ 初始化
export ZDOTDIR="$XDG_CONFIG_HOME/zsh"
export HISTFILE="$XDG_STATE_HOME/zsh/history"
# compinit 缓存路径需在 .zshrc 中配合使用：
#   mkdir -p "$XDG_CACHE_HOME/zsh"
#   autoload -Uz compinit && compinit -d "$XDG_CACHE_HOME/zsh/zcompdump-$ZSH_VERSION"

# Oh My Zsh 数据目录（Issue #9543: OMZ 不完全遵循 XDG，此变量为最佳缓解方案）
export ZSH="$XDG_DATA_HOME/oh-my-zsh"

# ===
# Shell History (Bash / Ash)
# ===
export HISTFILE="$XDG_STATE_HOME/bash/history"       # Bash history
# 如使用 ash: export HISTFILE="$XDG_STATE_HOME/ash/history"

# ===
# Development Tools
# ===
# Rust / Cargo
export CARGO_HOME="$XDG_DATA_HOME/cargo"

# Go
export GOPATH="$XDG_DATA_HOME/go"

# NPM
export NPM_CONFIG_USERCONFIG="$XDG_CONFIG_HOME/npm/npmrc"
export NPM_CONFIG_INIT_MODULE="$XDG_CONFIG_HOME/npm/config/npm-init.js"
export NPM_CONFIG_CACHE="$XDG_CACHE_HOME/npm"
export NPM_CONFIG_TMP="${XDG_RUNTIME_DIR:-/tmp}/npm"

# NVM
export NVM_DIR="$XDG_DATA_HOME/nvm"

# Gradle
export GRADLE_USER_HOME="$XDG_DATA_HOME/gradle"

# Elan (Lean)
export ELAN_HOME="$XDG_DATA_HOME/elan"

# Triton
export TRITON_HOME="$XDG_CACHE_HOME/triton"

# GNU Parallel
export PARALLEL_HOME="$XDG_CONFIG_HOME/parallel"

# Codex
export CODEX_HOME="$XDG_CONFIG_HOME/codex"

# Claude Code
export CLAUDE_CONFIG_DIR="$XDG_CONFIG_HOME/claude"

# ===
# Python History（自动适配版本）
# ===
if python3 -c "import sys; exit(0 if sys.version_info >= (3,13) else 1)" 2>/dev/null; then
    # Python >= 3.13: 原生支持 PYTHON_HISTORY
    export PYTHON_HISTORY="$XDG_STATE_HOME/python_history"
else
    # Python < 3.13: 需要 PYTHONSTARTUP 脚本
    export PYTHONSTARTUP="$XDG_CONFIG_HOME/python/pythonrc.py"
    # 注意：需手动创建该文件，内容参见 xdg-ninja 输出中的 pythonrc.py 脚本
fi

# ===
# Java / OpenJDK
# ===
# 部分 Java 应用不遵守此设置，但这是目前最佳方案
export _JAVA_OPTIONS="-Djava.util.prefs.userRoot=$XDG_CONFIG_HOME/java"

# ===
# GnuPG（⚠️ 高风险，迁移后务必验证 socket 路径）
# ===
export GNUPGHOME="$XDG_DATA_HOME/gnupg"

# ===
# Android SDK / ADB
# ===
export ANDROID_HOME="$XDG_DATA_HOME/android/sdk"
export ANDROID_USER_HOME="$XDG_DATA_HOME/android"
export ANDROID_AVD_HOME="$XDG_DATA_HOME/android/avd"
# adb alias（需放在 .zshrc 或 .bashrc 中，alias 不能在 .zshenv 中导出）
# alias adb='HOME="$XDG_DATA_HOME/android" adb'

# ===
# GTK / X11 / GUI
# ===
export GTK2_RC_FILES="$XDG_CONFIG_HOME/gtk-2.0/gtkrc"
# xrdb 需在 Xsession 或 .xinitrc 中调用：
#   xrdb -load "$XDG_CONFIG_HOME/X11/xresources"

# ===
# NVIDIA
# ===
export CUDA_CACHE_PATH="$XDG_CACHE_HOME/nv"
# nvidia-settings 需 alias：
# alias nvidia-settings='nvidia-settings --config="$XDG_CONFIG_HOME/nvidia/settings"'

# ===
# .NET / Dotnet
# ===
export DOTNET_CLI_HOME="$XDG_DATA_HOME/dotnet"

# ===
# Z (目录跳转工具)
# ===
export _Z_DATA="$XDG_DATA_HOME/z"

# ===
# Ren'Py
# ===
export RENPY_PATH_TO_SAVES="$XDG_DATA_HOME"

# ===
# LMMS（需 alias）
# ===
# alias lmms='lmms -c "$XDG_CONFIG_HOME/lmms/lmmsrc.xml"'
```
casuki@fedora:~/xdg-ninja (main) $ npm list -g --depth=0
/home/casuki/.nvm/versions/node/v24.14.0/lib
├── @anthropic-ai/claude-code@2.1.195
├── @github/copilot@1.0.36
├── @openai/codex@0.142.5
├── @playwright/cli@0.1.1
├── bun@1.3.12
├── corepack@0.34.6
├── npm@11.9.0
└── opencode-ai@1.17.15

casuki@fedora:~/xdg-ninja (main) $ cargo install --list
hyprshell v4.10.4:
    hyprshell
kitdraw v0.2.1:
    kitdraw
kitpdf v0.3.3:
    kitpdf
termusic v0.13.2:
    termusic
termusic-server v0.13.2:
    termusic-server
viu v1.6.1:
    viu
waycorner v0.2.3:
    waycorner