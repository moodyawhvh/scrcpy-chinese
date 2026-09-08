> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 相机

Android 12 及以上版本的设备支持相机投屏。

用相机代替设备屏幕进行采集:

```bash
scrcpy --video-source=camera
```

默认情况下,它会自动把[音源](audio.md#source)切换为麦克风(相当于同时传入了 `--audio-source=mic`)。

```bash
scrcpy --video-source=display  # 默认 --audio-source=output
scrcpy --video-source=camera   # 默认 --audio-source=mic
scrcpy --video-source=display --audio-source=mic    # 强制 屏幕+麦克风
scrcpy --video-source=camera --audio-source=output  # 强制 相机+设备音频输出
```

可以禁用音频:

```bash
# audio not captured at all
scrcpy --video-source=camera --no-audio
scrcpy --video-source=camera --no-audio --record=file.mp4

# audio captured and recorded, but not played
scrcpy --video-source=camera --no-audio-playback --record=file.mp4
```


## 列表

列出可用相机(及其声明的有效尺寸和帧率):

```bash
scrcpy --list-cameras
scrcpy --list-camera-sizes
```

_注意,尺寸和帧率只是声明值。它们并非在所有设备上都准确:有些声明了但实际不支持,有些没声明但实际支持。_


## 选择

可以显式传入相机 id(见 `--list-cameras` 列表):

```bash
scrcpy --video-source=camera --camera-id=0
```

也可以自动选择相机:

```bash
scrcpy --video-source=camera                           # 使用第一个相机
scrcpy --video-source=camera --camera-facing=front     # 使用第一个前置相机
scrcpy --video-source=camera --camera-facing=back      # 使用第一个后置相机
scrcpy --video-source=camera --camera-facing=external  # 使用第一个外接相机
```

指定了 `--camera-id` 时禁止使用 `--camera-facing`(id 已经确定了相机):

```bash
scrcpy --video-source=camera --camera-id=0 --camera-facing=front  # 报错
```


### 尺寸选择

可以显式传入相机尺寸:

```bash
scrcpy --video-source=camera --camera-size=1920x1080
```

给定的尺寸可以列在声明的有效尺寸中(`--list-camera-sizes`),也可以是任意其他值(部分设备支持任意尺寸):

```bash
scrcpy --video-source=camera --camera-size=1840x444
```

也可以自动选择一个已声明的有效尺寸(从 `list-camera-sizes` 列出的尺寸中选择)。

支持两种约束:
 - `-m`/`--max-size`(屏幕投屏已在用的参数),例如 `-m1920`;
 - `--camera-ar` 指定宽高比(`<num>:<den>`、`<value>` 或 `sensor`)。

一些例子:

```bash
scrcpy --video-source=camera                          # use the greatest width and the greatest associated height
scrcpy --video-source=camera -m1920                   # use the greatest width not above 1920 and the greatest associated height
scrcpy --video-source=camera --camera-ar=4:3          # use the greatest size with an aspect ratio of 4:3 (+/- 10%)
scrcpy --video-source=camera --camera-ar=1.6          # use the greatest size with an aspect ratio of 1.6 (+/- 10%)
scrcpy --video-source=camera --camera-ar=sensor       # use the greatest size with the aspect ratio of the camera sensor (+/- 10%)
scrcpy --video-source=camera -m1920 --camera-ar=16:9  # use the greatest width not above 1920 and the closest to 16:9 aspect ratio
```

指定了 `--camera-size` 时禁止使用 `-m`/`--max-size` 和 `--camera-ar`(尺寸由显式给定值决定):

```bash
scrcpy --video-source=camera --camera-size=1920x1080 -m3000  # 报错
```


## 旋转

要旋转采集到的视频,使用[视频方向](video.md#orientation)选项:

```bash
scrcpy --video-source=camera --camera-size=1920x1080 --orientation=90
```


## 帧率

默认按 Android 的默认帧率(30 fps)采集相机。

要配置其他帧率:

```bash
scrcpy --video-source=camera --camera-fps=60
```


## 高速采集

Android 相机 API 还支持[高速采集模式][high speed]。

该模式仅限特定的分辨率和帧率,可用 `--list-camera-sizes` 列出。

```bash
scrcpy --video-source=camera --camera-size=1920x1080 --camera-high-speed --camera-fps=240
```

[high speed]: https://developer.android.com/reference/android/hardware/camera2/CameraConstrainedHighSpeedCaptureSession


## 花括号展开小技巧

所有相机选项都以 `--camera-` 开头,如果你的 shell 支持[花括号展开][brace expansion](_bash_ 和 _zsh_ 都支持),可以偷个懒:

```bash
scrcpy --video-source=camera --camera-{facing=back,ar=16:9,high-speed,fps=120}
```

它会被展开为:

```bash
scrcpy --video-source=camera --camera-facing=back --camera-ar=16:9 --camera-high-speed --camera-fps=120
```

[brace expansion]: https://www.gnu.org/software/bash/manual/html_node/Brace-Expansion.html


## 手电筒

启动时用 `--camera-torch` 打开相机手电筒:

```
scrcpy --video-source=camera --camera-torch
```

也可以分别用 <kbd>MOD</kbd>+<kbd>t</kbd> 和 <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>t</kbd> 动态开关。


## 变焦

用 `--camera-zoom` 设置相机变焦:

```bash
scrcpy --video-source=camera --camera-zoom=1.5
```

也可以分别用 <kbd>MOD</kbd>+<kbd>↑</kbd> _(上)_ 和 <kbd>MOD</kbd>+<kbd>↓</kbd> _(下)_ 动态调节。


## 网络摄像头

在 Linux 上与 [V4L2](v4l2.md) 功能结合,Android 设备相机可以在电脑上当网络摄像头用。
