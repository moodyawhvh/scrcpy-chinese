> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 录制

投屏的同时录制视频和音频流:

```bash
scrcpy --record=file.mp4
scrcpy -r file.mkv
```

只录视频:

```bash
scrcpy --no-audio --record=file.mp4
```

只录音频:

```bash
scrcpy --no-video --record=file.opus
scrcpy --no-video --audio-codec=aac --record=file.aac
scrcpy --no-video --audio-codec=flac --record=file.flac
scrcpy --no-video --audio-codec=raw --record=file.wav
# opus、aac 和 flac 也支持 .m4a/.mp4 和 .mka/.mkv
```

时间戳是在设备上采集的,所以[包延迟变化][packet delay variation]不会影响录制文件,录出来的内容始终干净(当然,前提是用 `--record`,而不是在电脑上录 scrcpy 窗口和音频输出)。

[packet delay variation]: https://en.wikipedia.org/wiki/Packet_delay_variation


## 格式

视频和音频流在设备上编码,但在客户端一侧封装。支持多种格式(容器):
 - MP4(`.mp4`、`.m4a`、`.aac`)
 - Matroska(`.mkv`、`.mka`)
 - OPUS(`.opus`)
 - FLAC(`.flac`)
 - WAV(`.wav`)

容器会根据文件名自动选择。

也可以显式指定容器(此时文件名不需要以已知扩展名结尾):

```bash
scrcpy --record=file --record-format=mkv
```


## 旋转

录制时可以让视频旋转。参见[视频方向](video.md#orientation)。


## 不播放

录制时禁用播放和控制:

```bash
scrcpy --no-playback --no-control --record=file.mp4
```

也可以分别禁用视频和音频播放:

```bash
# Record both video and audio, but only play video
scrcpy --record=file.mkv --no-audio-playback
```

连窗口也一并禁用:

```bash
scrcpy --no-playback --no-window --record=file.mp4
# interrupt recording with Ctrl+C
```

## 时长限制

限制录制时长:

```bash
scrcpy --record=file.mkv --time-limit=20  # 单位为秒
```

`--time-limit` 不只限录制,对普通投屏同样生效:

```bash
scrcpy --time-limit=20
```
