> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 常见问题(FAQ)

[使用其他语言阅读](#翻译)

这里收录了用户反馈过的常见问题及其现状。

如果遇到任何错误,第一步是先升级到最新版本。


## `adb` 与 USB 问题

`scrcpy` 会执行 `adb` 命令来初始化与设备的连接。如果 `adb` 失败,scrcpy 就无法工作。

这类问题通常不是 _scrcpy_ 本身的 bug,而是你的环境有问题。


### 找不到 `adb`

你需要让 `adb` 可以通过 `PATH` 访问到。

在 Windows 上,当前目录会包含在 `PATH` 中,而且发行包里自带了 `adb.exe`,所以开箱即用。


### 检测不到设备

>     ERROR: Could not find any ADB device

检查你是否正确启用了 [adb 调试][enable-adb]。

你的设备必须能被 `adb` 检测到:

```
adb devices
```

如果设备检测不到,在 Windows 上你可能需要安装一些[驱动程序][drivers]。Google 设备另有单独的 [USB 驱动][google-usb-driver]。

[enable-adb]: https://developer.android.com/studio/command-line/adb.html#Enabling
[drivers]: https://developer.android.com/studio/run/oem-usb.html
[google-usb-driver]: https://developer.android.com/studio/run/win-usb


### 设备未授权

>     ERROR: Device is unauthorized:
>     ERROR:     -->   (usb)  0123456789abcdef          unauthorized
>     ERROR: A popup should open on the device to request authorization.

连接时,设备上应该会弹出一个授权弹窗,你必须允许 USB 调试。

如果没有弹出,请参考 [stackoverflow][device-unauthorized]。

[device-unauthorized]: https://stackoverflow.com/questions/23081263/adb-android-device-unauthorized


### 连接了多台设备

如果同时连接了多台设备,会遇到如下错误:

>     ERROR: Multiple (2) ADB devices:
>     ERROR:     -->   (usb)  0123456789abcdef                device  Nexus_5
>     ERROR:     --> (tcpip)  192.168.1.5:5555                device  GM1913
>     ERROR: Select a device via -s (--serial), -d (--select-usb) or -e (--select-tcpip)

这时,你可以指定要投屏的设备标识:

```bash
scrcpy -s 0123456789abcdef
```

或者选择唯一的 USB(或 TCP/IP)设备:

```bash
scrcpy -d  # USB 设备
scrcpy -e  # TCP/IP 设备
```

注意,如果你的设备通过 TCP/IP 连接,可能会看到这样的提示:

>     adb: error: more than one device/emulator
>     ERROR: "adb reverse" returned with value 1
>     WARN: 'adb reverse' failed, fallback to 'adb forward'

这是预期行为(由旧版 Android 的一个 bug 导致,见 [#5]),此时 scrcpy 会回退到另一种方式,通常可以正常工作。

[#5]: https://github.com/Genymobile/scrcpy/issues/5


### adb 版本冲突

>     adb server version (41) doesn't match this client (39); killing...

当你同时使用多个 `adb` 版本时会出现这个错误。你必须找出是哪个程序在用另一个 `adb` 版本,并让所有地方都使用同一个 `adb` 版本。

你可以覆盖其他程序里的 `adb` 可执行文件,或者通过设置 `ADB` 环境变量,让 _scrcpy_ 使用指定的 `adb`:

```bash
# 在 bash 中
export ADB=/path/to/your/adb
scrcpy
```

```cmd
:: 在 cmd 中
set ADB=C:\path\to\your\adb.exe
scrcpy
```

```powershell
# 在 PowerShell 中
$env:ADB = 'C:\path\to\your\adb.exe'
scrcpy
```


### 设备断开连接

如果 _scrcpy_ 自己退出并提示 "Device disconnected",说明 `adb` 连接已经被关闭了。

换一根 USB 线,或者插到另一个 USB 口试试。参见 [#281] 和 [#283]。

[#281]: https://github.com/Genymobile/scrcpy/issues/281
[#283]: https://github.com/Genymobile/scrcpy/issues/283


## Windows 上的 OTG 问题

在 Windows 上,如果 `scrcpy --otg`(或 `--keyboard=aoa`/`--mouse=aoa`)报错:

>     ERROR: Could not find any USB device

(或者只能检测到无关的 USB 设备),那多半是驱动问题。

请阅读 [#3654],尤其是[这条评论][#3654-comment1]、[下一条][#3654-comment2]和[这一条][#3654-comment3]。

[#3654]: https://github.com/Genymobile/scrcpy/issues/3654
[#3654-comment1]: https://github.com/Genymobile/scrcpy/issues/3654#issuecomment-1369278232
[#3654-comment2]: https://github.com/Genymobile/scrcpy/issues/3654#issuecomment-1369295011
[#3654-comment3]: https://github.com/Genymobile/scrcpy/issues/3654#issuecomment-2613219725


## 控制问题

### 鼠标和键盘不起作用

部分设备上,你需要开启一个选项来允许[模拟输入][simulating input]。在开发者选项中启用:

> **USB 调试(安全设置)**  
> _允许通过 USB 调试授予权限并模拟输入_

设置后需要重启设备才能生效。

[simulating input]: https://github.com/Genymobile/scrcpy/issues/70#issuecomment-373286323


### 特殊字符无法输入

默认的文本注入方式只支持 ASCII 字符。有个小技巧可以额外注入部分[带变音符号的字符][accented-characters],但也仅此而已。参见 [#37]。

要彻底避开这个问题,可以把[键盘模式改为模拟物理键盘][hid]。

[accented-characters]: https://blog.rom1v.com/2018/03/introducing-scrcpy/#handle-accented-characters
[#37]: https://github.com/Genymobile/scrcpy/issues/37
[hid]: doc/keyboard.md#physical-keyboard-simulation


## 客户端问题

### Wayland 相关问题

默认情况下,SDL 在 Linux 上使用 x11。可以通过 `SDL_VIDEODRIVER` 环境变量更换[视频驱动][video driver]:

[video driver]: https://wiki.libsdl.org/FAQUsingSDL#how_do_i_choose_a_specific_video_driver

```bash
export SDL_VIDEODRIVER=wayland
scrcpy
```

在某些发行版上(至少 Fedora),需要手动安装 `libdecor` 包。

参见 issue [#2554] 和 [#2559]。

[#2554]: https://github.com/Genymobile/scrcpy/issues/2554
[#2559]: https://github.com/Genymobile/scrcpy/issues/2559


### KWin 合成器崩溃

在 Plasma 桌面上,scrcpy 运行期间合成器会被禁用。

临时解决办法是[取消勾选 "Block compositing"][kwin]。

[kwin]: https://github.com/Genymobile/scrcpy/issues/114#issuecomment-378778613


## 崩溃

### 异常

如果出现任何与 `MediaCodec` 相关的异常:

```
ERROR: Exception on thread Thread[main,5,main]
java.lang.IllegalStateException
        at android.media.MediaCodec.native_dequeueOutputBuffer(Native Method)
```

可以尝试换一个[编码器](doc/video.md#encoder)。


## 翻译

本 FAQ 的其他语言翻译可在 [wiki] 中查看。

[wiki]: https://github.com/Genymobile/scrcpy/wiki

只有本 FAQ 文件保证始终是最新版本。
