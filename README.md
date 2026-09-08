<div align="center">

# scrcpy 中文翻译版

**[中文版] scrcpy — 通过 USB 或 TCP/IP 在电脑上显示并控制 Android 设备,无需 root**

[![原项目](https://img.shields.io/badge/原项目-Genymobile--scrcpy-blue?style=flat-square&logo=github)](https://github.com/Genymobile/scrcpy)
[![中文文档](https://img.shields.io/badge/中文文档-README.zh--CN.md-orange?style=flat-square)](README.zh-CN.md)
[![GitHub Stars](https://img.shields.io/github/stars/Genymobile/scrcpy?style=flat-square&label=原项目Stars)](https://github.com/Genymobile/scrcpy/stargazers)
[![微信联系](https://img.shields.io/badge/微信-uaycar-brightgreen?style=flat-square&logo=wechat)](#)

</div>

---

> 这是 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 的中文翻译版本。
> 完整源代码请访问原项目:https://github.com/Genymobile/scrcpy

**代部署 / 定制服务 / 技术咨询 请添加微信:uaycar**

---

## 📖 项目简介

scrcpy(发音为 "screen copy")是一款免费开源的 Android 设备投屏与控制工具:它通过 USB 或 TCP/IP(无线)把手机的画面和声音镜像到电脑上,并用电脑的键盘和鼠标直接操控设备。它不需要 root 权限,也不必在手机上安装任何 App,同时支持 Linux、Windows 和 macOS。目前最新版本为 v4.1,请务必只从官方仓库获取,谨防冒名网站。

## ✨ 主要特性

- **轻量**:原生应用,只显示设备屏幕本身
- **高性能**:30~120fps(取决于设备性能)
- **高画质**:1920×1080 或更高
- **低延迟**:约 35~70ms
- **秒开**:约 1 秒即可显示第一帧画面
- **无侵入**:不会在 Android 设备上留下任何安装内容
- **清爽**:无账号、无广告、无需联网
- **音频转发**(Android 11+)、**录屏**、**虚拟显示器**、**熄屏镜像**
- **双向复制粘贴**、**摄像头镜像**(Android 12+)、**V4L2 虚拟摄像头**(仅 Linux)
- **HID 物理键盘 / 鼠标模拟**、**手柄支持**、**OTG 模式**

## 📁 文件说明

| 文件 | 说明 |
|:-----|:-----|
| README.md | 本文件(中文简介) |
| README.zh-CN.md | 详细中文文档(完整汉化) |

## 🚀 快速开始

1. **设备要求**:Android 5.0(API 21)及以上;音频转发需要 Android 11+。
2. **开启 USB 调试**:在手机开发者选项中启用 USB 调试(部分小米机型还需开启"USB 调试(安全设置)"并重启)。
3. **安装 scrcpy**:Windows 直接下载官方压缩包,Linux 使用发行版包管理器,macOS 执行 `brew install scrcpy`(详见原项目 doc 目录)。
4. **USB 连接运行**:手机插上电脑后直接执行:

   ```bash
   scrcpy
   ```

5. **无线连接**:手机与电脑处于同一局域网时执行:

   ```bash
   scrcpy --tcpip=192.168.1.1
   ```

6. **常用技巧**:_右键_ = 返回,_中键_ = 主页,<kbd>Alt</kbd>+<kbd>f</kbd> = 全屏;`scrcpy -m1024` 可降低分辨率大幅提升流畅度。
7. **进阶示例**(H.265 高画质录制 / 虚拟显示器启动应用 / OTG 模式):

   ```bash
   scrcpy --video-codec=h265 --max-size=1920 --max-fps=60 --no-audio --keyboard=uhid
   scrcpy --new-display=1920x1080 --start-app=org.videolan.vlc
   scrcpy --otg
   ```

完整源代码与最新版本请访问原项目:https://github.com/Genymobile/scrcpy

更多参数与文档(视频、音频、控制、快捷键、录屏等)见 [README.zh-CN.md](README.zh-CN.md)。**代部署 / 定制服务 / 技术咨询 请添加微信:uaycar**

## 📞 联系方式

**代部署 / 定制服务 / 技术咨询 请添加微信:uaycar**

---

本项目为 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 的中文翻译版本,所有代码版权归原项目作者所有,遵循其原始许可证(Apache License 2.0)。

**如果觉得有用,请给原项目点个 Star!** ⭐
