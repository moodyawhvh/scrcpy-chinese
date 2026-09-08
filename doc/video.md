> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 视频

## 画面来源

默认情况下,scrcpy 投屏设备屏幕。

也可以改为采集设备相机。

参见专门的[相机](camera.md)页面。


## 尺寸

默认情况下,scrcpy 尝试以 Android 设备的分辨率投屏。

用更低的分辨率投屏可以提升性能。要把宽度和高度限制在某个最大值以内(这里以 1024 为例):

```bash
scrcpy --max-size=1024
scrcpy -m 1024   # 简写
```

另一边的尺寸会按比例计算,以保持 Android 设备的宽高比(flex display 除外)。这样,1920×1080 的设备会以 1024×576 投屏。

如果编码失败,scrcpy 会自动用更低的分辨率重试(除非启用了 `--no-downsize-on-error`)。

相机投屏时,`--max-size` 的值用于在可用分辨率中选择相机源尺寸。

尺寸会被取整到编码器要求的_对齐_值的倍数——这是一个 2 的幂(1、2、4、8 或 16),视频的宽和高都必须是它的倍数。

可以强制对齐到一个最小值。例如,强制宽高都是 8 的倍数:

```bash
scrcpy --min-size-alignment=8
```


## 比特率

默认视频比特率为 8 Mbps。修改方式:

```bash
scrcpy --video-bit-rate=2M
scrcpy --video-bit-rate=2000000  # 等价
scrcpy -b 2M                     # 简写
```


## 帧率

可以限制采集帧率:

```bash
scrcpy --max-fps=15
```

实际采集帧率可以打印到控制台:

```bash
scrcpy --print-fps
```

也可以随时用 <kbd>MOD</kbd>+<kbd>i</kbd> 开关(参见[快捷键](shortcuts.md))。

帧率本质上是可变的:只有屏幕内容变化时才会产生新帧。例如,如果设备上以 24fps 全屏播放视频,scrcpy 中每秒不会超过 24 帧。


## 编解码器

可以选择视频编解码器,可选值为 `h264`(默认)、`h265`、`av1`、`vp8` 和 `vp9`:

```bash
scrcpy --video-codec=h264  # 默认
scrcpy --video-codec=h265
scrcpy --video-codec=av1
scrcpy --video-codec=vp8
scrcpy --video-codec=vp9
```

H265 画质可能更好,但 H264 延迟更低。当前 Android 设备上 AV1 编码器并不常见。

进阶用法:要给 [`MediaFormat`] 传递任意参数,请查阅 man 手册页或 `scrcpy --help` 中的 `--video-codec-options`。

[`MediaFormat`]: https://developer.android.com/reference/android/media/MediaFormat


## 编码器

设备上可能有多个编码器可用,可以用以下命令列出:

```bash
scrcpy --list-encoders
```

有时默认编码器会有问题甚至崩溃,这时换个编码器会很有用:

```bash
scrcpy --video-codec=h264 --video-encoder=OMX.qcom.video.encoder.avc
```


## 方向

旋转可以在 3 个不同层面生效:
 - [快捷键](shortcuts.md) <kbd>MOD</kbd>+<kbd>r</kbd> 请求设备在竖屏和横屏之间切换(当前运行的应用如果不支持请求的方向,可以拒绝)。
 - `--capture-orientation` 改变投屏采集方向(设备发送到电脑的视频方向)。这会影响录制。
 - `--orientation` 在客户端一侧生效,影响显示和录制。显示部分可以通过[快捷键](shortcuts.md)动态修改。

以指定方向采集视频:

```bash
scrcpy --capture-orientation=0
scrcpy --capture-orientation=90       # 顺时针 90°
scrcpy --capture-orientation=180      # 180°
scrcpy --capture-orientation=270      # 顺时针 270°
scrcpy --capture-orientation=flip0    # 水平翻转
scrcpy --capture-orientation=flip90   # 水平翻转 + 顺时针 90°
scrcpy --capture-orientation=flip180  # 水平翻转 + 180°
scrcpy --capture-orientation=flip270  # 水平翻转 + 顺时针 270°
```

在值前加 `@` 可以锁定采集方向,这样设备的物理旋转不会改变采集到的视频方向:

```bash
scrcpy --capture-orientation=@         # 锁定为初始方向
scrcpy --capture-orientation=@0        # 锁定为 0°
scrcpy --capture-orientation=@90       # 锁定为顺时针 90°
scrcpy --capture-orientation=@180      # 锁定为 180°
scrcpy --capture-orientation=@270      # 锁定为顺时针 270°
scrcpy --capture-orientation=@flip0    # 锁定为水平翻转
scrcpy --capture-orientation=@flip90   # 锁定为水平翻转 + 顺时针 90°
scrcpy --capture-orientation=@flip180  # 锁定为水平翻转 + 180°
scrcpy --capture-orientation=@flip270  # 锁定为水平翻转 + 顺时针 270°
```

采集方向的变换在 `--crop` 之后、`--angle` 之前生效。

为视频定向(客户端一侧):

```bash
scrcpy --orientation=0
scrcpy --orientation=90       # 顺时针 90°
scrcpy --orientation=180      # 180°
scrcpy --orientation=270      # 顺时针 270°
scrcpy --orientation=flip0    # 水平翻转
scrcpy --orientation=flip90   # 水平翻转 + 顺时针 90°
scrcpy --orientation=flip180  # 垂直翻转(水平翻转 + 180°)
scrcpy --orientation=flip270  # 水平翻转 + 顺时针 270°
```

如有需要,可以通过 `--display-orientation` 和 `--record-orientation` 分别为显示和录制设置方向。

录制文件的旋转是通过向 MP4 或 MKV 目标文件写入显示变换矩阵实现的。翻转不受支持,所以录制时只允许前四个值。


## 角度

以自定义角度旋转视频内容(单位为度,顺时针):

```bash
scrcpy --angle=23
```

旋转中心是可见区域的中心。

这个变换在 `--crop` 和 `--capture-orientation` 之后生效。


## 裁剪

可以裁剪设备屏幕,只投屏屏幕的一部分。

例如,只投屏 Oculus Go 的一只眼睛:

```bash
scrcpy --crop=1224:1440:0:0   # 1224x1440,偏移 (0,0)
```

这些值以设备自然方向表示(手机为竖屏,平板为横屏)。

裁剪在 `--capture-orientation` 和 `--angle` 之前执行。

对屏幕投屏,`--max-size` 在裁剪之后应用;对相机,`--max-size` 先应用(因为它选择的是源尺寸,而不是缩放内容)。


## 显示器

如果 Android 设备上有多个显示器,可以选择要投屏的显示器:

```bash
scrcpy --display-id=1
```

可以用以下命令获取显示器 id 列表:

```bash
scrcpy --list-displays
```

副屏只有在设备运行 Android 10 及以上时才可控制(否则只能只读投屏)。

还可以创建[虚拟显示器](virtual-display.md)。


## 缓冲

默认没有视频缓冲,以获得尽可能低的延迟。

可以增加缓冲来延迟视频流、补偿网络抖动,使播放更平滑(参见 [#2464])。

[#2464]: https://github.com/Genymobile/scrcpy/issues/2464

显示、[v4l2 输出](v4l2.md#buffering)和[音频](audio.md#buffering)播放可以各自独立配置:

```bash
scrcpy --video-buffer=50     # 为视频播放增加 50ms 缓冲
scrcpy --audio-buffer=200    # 为音频播放设置 200ms 缓冲
scrcpy --v4l2-buffer=300     # 为 v4l2 输出增加 300ms 缓冲
```

它们可以同时使用:

```bash
scrcpy --video-buffer=50 --v4l2-buffer=300
```


## 不播放

可以在电脑上不播放视频或音频,只采集 Android 设备。[录制](recording.md)或启用 [v4l2](#video4linux) 时很有用:

```bash
scrcpy --v4l2-sink=/dev/video2 --no-playback
scrcpy --record=file.mkv --no-playback
# 用 Ctrl+C 中断
```

也可以分别禁用视频和音频播放:

```bash
# 视频发给 V4L2 但不播放,音频照常播放
scrcpy --v4l2-sink=/dev/video2 --no-video-playback

# 同时录制视频和音频,但只播放视频
scrcpy --record=file.mkv --no-audio-playback
```


## 禁用视频

完全禁用视频转发,只转发音频:

```bash
scrcpy --no-video
```


## Video4Linux

参见专门的 [Video4Linux](v4l2.md) 页面。
