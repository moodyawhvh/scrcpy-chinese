<div align="center">

# scrcpy 中文文档

**scrcpy — 通过 USB 或 TCP/IP 在电脑上显示并控制 Android 设备**

[![原项目](https://img.shields.io/badge/原项目-Genymobile--scrcpy-blue?style=flat-square&logo=github)](https://github.com/Genymobile/scrcpy)
[![简介](https://img.shields.io/badge/返回-README.md-blue?style=flat-square)](README.md)
[![微信联系](https://img.shields.io/badge/微信-uaycar-brightgreen?style=flat-square&logo=wechat)](#)

</div>

---

> [!WARNING]
> **https://github.com/Genymobile/scrcpy 是本项目唯一的官方来源。不要从不明网站下载 scrcpy 安装包,即使对方名字里带 "scrcpy"。**

**代部署 / 定制服务 / 技术咨询 请添加微信:uaycar**

## 简介

scrcpy(发音为 "**scr**een **c**o**py**")可以将通过 USB 或 [TCP/IP](doc/connection.md#tcpip-wireless) 连接的 Android 设备(画面和声音)镜像到电脑上,并允许用电脑的键盘和鼠标进行控制。它**不需要 root 权限**,也**不需要在设备上安装任何 App**,支持 Linux、Windows 和 macOS。

它专注于:

- **轻量**:原生应用,只显示设备屏幕
- **性能**:30~120fps,取决于设备
- **画质**:1920×1080 或更高
- **低延迟**:约 [35~70ms][lowlatency]
- **快速启动**:约 1 秒显示第一帧画面
- **无侵入**:设备上不会残留任何东西
- **用户友好**:无账号、无广告、无需联网
- **自由**:自由开源软件

[lowlatency]: https://github.com/Genymobile/scrcpy/pull/646

功能一览:

- [音频转发](doc/audio.md)(Android 11+)
- [录屏](doc/recording.md)
- [虚拟显示器](doc/virtual-display.md)
- 设备[熄屏状态](doc/device.md#turn-screen-off)下镜像
- [双向复制粘贴](doc/control.md#copy-paste)
- [可配置画质](doc/video.md)
- [摄像头镜像](doc/camera.md)(Android 12+)
- 作为 [V4L2 虚拟摄像头](doc/v4l2.md)(仅 Linux)
- 物理键盘 / 鼠标模拟(HID,见 [键盘][hid-keyboard] 与 [鼠标][hid-mouse])
- [手柄支持](doc/gamepad.md)
- [OTG 模式](doc/otg.md)
- 更多……

[hid-keyboard]: doc/keyboard.md#physical-keyboard-simulation
[hid-mouse]: doc/mouse.md#physical-keyboard-simulation

## 前置条件

- 设备需要 Android 5.0(API 21)及以上;音频转发需要 Android 11+(API 30)及以上。
- 请确保已在设备上[开启 USB 调试][enable-adb]。

[enable-adb]: https://developer.android.com/studio/debug/dev-options#enable

部分设备(尤其是小米)可能报错:

```
Injecting input events requires the caller (or the source of the instrumentation, if any) to have the INJECT_EVENTS permission.
```

此时需要额外开启 `USB 调试(安全设置)` 选项(与"USB 调试"是不同的一项),开启后重启手机即可正常用键鼠控制。注意:[OTG 模式](doc/otg.md)下运行 scrcpy 不需要 USB 调试。

## 获取应用

- [Linux](doc/linux.md)
- [Windows](doc/windows.md)(请阅读[运行方法](doc/windows.md#run))
- [macOS](doc/macos.md)

## 必知技巧

- 降低分辨率可大幅提升性能([`scrcpy -m1024`](doc/video.md#size))
- [_鼠标右键_](doc/mouse.md#mouse-bindings) 触发 **返回**
- [_鼠标中键_](doc/mouse.md#mouse-bindings) 触发 **主页**
- <kbd>Alt</kbd>+<kbd>f</kbd> 切换[全屏](doc/window.md#fullscreen)
- 还有许多其他[快捷键](doc/shortcuts.md)

## 使用示例

可选项非常多,[单独的文档页面](#用户文档)有完整说明,这里只列几个常见例子。

- 以 H.265(更高画质)采集屏幕,限制尺寸 1920、帧率 60fps,关闭音频,并模拟物理键盘控制设备:

  ```bash
  scrcpy --video-codec=h265 --max-size=1920 --max-fps=60 --no-audio --keyboard=uhid
  scrcpy --video-codec=h265 -m1920 --max-fps=60 --no-audio -K  # 简写
  ```

- 在新建的虚拟显示器(与手机屏幕独立)中启动 VLC:

  ```bash
  scrcpy --new-display=1920x1080 --start-app=org.videolan.vlc
  ```

- 在新建的 _flex_ 显示器中用 H.265、16Mbps 码率启动 VLC,并保持显示器不熄屏:

  ```bash
  scrcpy --new-display -x --keep-active --start-app=org.videolan.vlc --video-codec=h265 -b16M
  ```

- 以 H.265、1920x1080 录制设备摄像头画面(含麦克风)到 MP4 文件:

  ```bash
  scrcpy --video-source=camera --video-codec=h265 --camera-size=1920x1080 --record=file.mp4
  ```

- 采集设备前置摄像头并作为电脑摄像头输出(仅 Linux):

  ```bash
  scrcpy --video-source=camera --camera-size=1920x1080 --camera-facing=front --v4l2-sink=/dev/video2 --no-playback
  ```

- 不投屏,仅用模拟的物理键盘鼠标控制设备(无需 USB 调试):

  ```bash
  scrcpy --otg
  ```

- 使用插在电脑上的手柄控制设备:

  ```bash
  scrcpy --gamepad=uhid
  scrcpy -G  # 简写
  ```

## 用户文档

应用提供了大量功能和配置项,详见以下页面:

- [连接](doc/connection.md)
- [视频](doc/video.md)
- [音频](doc/audio.md)
- [控制](doc/control.md)
- [键盘](doc/keyboard.md)
- [鼠标](doc/mouse.md)
- [手柄](doc/gamepad.md)
- [设备](doc/device.md)
- [窗口](doc/window.md)
- [录屏](doc/recording.md)
- [虚拟显示器](doc/virtual-display.md)
- [隧道](doc/tunnels.md)
- [OTG](doc/otg.md)
- [摄像头](doc/camera.md)
- [Video4Linux](doc/v4l2.md)
- [快捷键](doc/shortcuts.md)

## 相关资源

- [常见问题 FAQ](FAQ.md)
- [多语言翻译][wiki](不一定保持最新)
- [构建说明](doc/build.md)
- [开发者文档](doc/develop.md)
- [验证发布签名](doc/verify-release.md)

[wiki]: https://github.com/Genymobile/scrcpy/wiki

## 相关文章

- [Introducing scrcpy(项目介绍)](https://blog.rom1v.com/2018/03/introducing-scrcpy/)
- [Scrcpy now works wirelessly(无线连接)](https://www.genymotion.com/blog/open-source-project-scrcpy-now-works-wirelessly/)
- [Scrcpy 2.0, with audio(支持音频)](https://blog.rom1v.com/2023/03/scrcpy-2-0-with-audio/)

## 联系与反馈

如需反馈 Bug、提出功能建议或一般性提问,可以到原项目提交 [issue](https://github.com/Genymobile/scrcpy/issues)。提交 Bug 前建议先阅读 [FAQ](FAQ.md),也许能立刻找到解决办法。

其他渠道:

- Reddit:[`r/scrcpy`](https://www.reddit.com/r/scrcpy)
- BlueSky:[`@scrcpy.bsky.social`](https://bsky.app/profile/scrcpy.bsky.social)
- Twitter:[`@scrcpy_app`](https://twitter.com/scrcpy_app)

## 捐赠支持

scrcpy 的作者与维护者是 [@rom1v](https://github.com/rom1v)。如果你喜欢这个应用,可以[支持他的开源工作](https://blog.rom1v.com/about/#support-my-open-source-work):GitHub Sponsors / Liberapay / PayPal。

## 许可证

    Copyright (C) 2018 Genymobile
    Copyright (C) 2018-2026 Romain Vimont

    基于 Apache License 2.0 授权。
    完整许可证文本见原项目 LICENSE 文件。

---

> **版权声明**:本文档是 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) README 的中文翻译版本,仅供学习交流使用。所有代码及原文档版权归原项目作者(Genymobile / Romain Vimont)所有,遵循其原始许可证(Apache License 2.0)。

**代部署 / 定制服务 / 技术咨询 请添加微信:uaycar**

**如果觉得有用,请给原项目点个 Star!** ⭐ → https://github.com/Genymobile/scrcpy
