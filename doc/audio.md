> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 音频

Android 11 及以上版本的设备支持音频转发,并且默认开启:

 - **Android 12 及以上**:开箱即用。
 - **Android 11**:启动 scrcpy 时需要确保设备屏幕已解锁。scrcpy 会短暂弹出一个假弹窗,让系统以为 shell 应用处于前台,否则音频采集会失败。
 - **Android 10 及以下**:无法采集音频,音频功能会自动禁用。

如果音频采集失败,投屏会继续,只是只有视频(因为音频默认开启,音频不可用就让 scrcpy 直接失败是不可接受的),除非设置了 `--require-audio`。


## 关闭音频

禁用音频:

```bash
scrcpy --no-audio
```

如果只想禁用音频播放,参见[不播放](video.md#no-playback)。

## 仅音频

只想播放音频时,禁用视频和控制:

```bash
scrcpy --no-video --no-control
```

不显示窗口,只播放音频:

```bash
# --no-window 隐含了 --no-video 和 --no-control
scrcpy --no-window
# 用 Ctrl+C 中断
```

没有视频时,音频延迟通常无关紧要,因此可以加大[缓冲](#缓冲),把卡顿降到最低:

```bash
scrcpy --no-video --audio-buffer=200
```

## 音源

默认转发设备的音频输出。

也可以改为采集设备麦克风:

```bash
scrcpy --audio-source=mic
```

例如,把设备当录音笔用,直接在电脑上录制采集结果:

```bash
scrcpy --audio-source=mic --no-video --no-playback --record=file.opus
```

可用的音源有很多:

 - `output`(默认):转发全部音频输出,并禁用设备端的播放(映射到 [`REMOTE_SUBMIX`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#REMOTE_SUBMIX))。
 - `playback`:采集音频播放(Android 应用可以主动拒绝,因此不一定能采集到全部输出)。
 - `mic`:采集麦克风(映射到 [`MIC`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#MIC))。
 - `mic-unprocessed`:采集麦克风未处理(原始)的声音(映射到 [`UNPROCESSED`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#UNPROCESSED))。
 - `mic-camcorder`:采集为录像调校的麦克风,如可用则与相机方向一致(映射到 [`CAMCORDER`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#CAMCORDER))。
 - `mic-voice-recognition`:采集为语音识别调校的麦克风(映射到 [`VOICE_RECOGNITION`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#VOICE_RECOGNITION))。
 - `mic-voice-communication`:采集为语音通信调校的麦克风(例如可用时会利用回声消除或自动增益)(映射到 [`VOICE_COMMUNICATION`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#VOICE_COMMUNICATION))。
 - `voice-call`:采集语音通话(映射到 [`VOICE_CALL`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#VOICE_CALL))。
 - `voice-call-uplink`:仅采集语音通话上行(映射到 [`VOICE_UPLINK`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#VOICE_UPLINK))。
 - `voice-call-downlink`:仅采集语音通话下行(映射到 [`VOICE_DOWNLINK`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#VOICE_DOWNLINK))。
 - `voice-performance`:采集用于实时演出(K歌)处理的音频,同时包含麦克风和设备播放(映射到 [`VOICE_PERFORMANCE`](https://developer.android.com/reference/android/media/MediaRecorder.AudioSource#VOICE_PERFORMANCE))。

### 复制播放

还有一种设备音频采集方式(仅 Android 13 及以上):

```bash
scrcpy --audio-source=playback
```

配合 `--audio-dup`,这个音源可以在投屏的同时让声音继续在设备上播放:

```bash
scrcpy --audio-source=playback --audio-dup
# 或者简写:
scrcpy --audio-dup  # 隐含 --audio-source=playback
```

但它要求 Android 13,而且 Android 应用可以主动拒绝(于是不会被采集)。


参见 [#4380](https://github.com/Genymobile/scrcpy/issues/4380)。


## 编解码器

可以选择音频编解码器,可选值为 `opus`(默认)、`aac`、`flac` 和 `raw`(未压缩的 16 位小端 PCM):

```bash
scrcpy --audio-codec=opus  # 默认
scrcpy --audio-codec=aac
scrcpy --audio-codec=flac
scrcpy --audio-codec=raw
```

特别是,如果你遇到如下错误:

> Failed to initialize audio/opus, error 0xfffffffe

说明你的设备没有 Opus 编码器:试试 `scrcpy --audio-codec=aac`。

进阶用法:要给 [`MediaFormat`] 传递任意参数,请查阅 man 手册页或 `scrcpy --help` 中的 `--audio-codec-options`。

例如,修改 [FLAC 压缩等级][FLAC compression level]:

```bash
scrcpy --audio-codec=flac --audio-codec-options=flac-compression-level=8
```

[`MediaFormat`]: https://developer.android.com/reference/android/media/MediaFormat
[FLAC compression level]: https://developer.android.com/reference/android/media/MediaFormat#KEY_FLAC_COMPRESSION_LEVEL


## 编码器

设备上可能有多个编码器可用,可以用以下命令列出:

```bash
scrcpy --list-encoders
```

选择指定的编码器:

```bash
scrcpy --audio-codec=opus --audio-encoder='c2.android.opus.encoder'
```


## 比特率

默认音频比特率为 128Kbps。修改方式:

```bash
scrcpy --audio-bit-rate=64K
scrcpy --audio-bit-rate=64000  # 等价
```

_该参数对 RAW 音频编解码器(`--audio-codec=raw`)无效。_


## 缓冲

音频缓冲不可避免。它必须足够小,延迟才能接受;又必须足够大,才能尽量减少缓冲区下溢(会导致声音卡顿)。

默认缓冲大小为 50ms,可以调整:

```bash
scrcpy --audio-buffer=40   # 小于默认值
scrcpy --audio-buffer=100  # 大于默认值
```

注意,这个选项改变的是_目标_缓冲量。实际缓冲可能达不到这个目标(典型情况是频繁发生缓冲下溢时)。

如果你不与设备交互(比如只是看视频),更高的延迟([视频](video.md#buffering)和音频都是)可能反而更好,可以避免卡顿、让播放更平滑:

```bash
scrcpy --video-buffer=200 --audio-buffer=200
```

还可以配置另一个音频缓冲(音频输出缓冲),默认 10ms。除非有充分的理由,不要改动这项设置(参见[机器音和爆音问题][#3793])。例如:

```bash
scrcpy --audio-output-buffer=5
```

[#3793]: https://github.com/Genymobile/scrcpy/issues/3793
