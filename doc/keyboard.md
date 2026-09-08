> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 键盘

有几种键盘输入模式可选:

 - `--keyboard=sdk`(默认)
 - `--keyboard=uhid`(或 `-K`):利用设备上的 UHID 内核模块模拟物理 HID 键盘
 - `--keyboard=aoa`:利用 AOAv2 协议模拟物理 HID 键盘
 - `--keyboard=disabled`

默认使用 `sdk`,但如果你经常用 scrcpy,建议改用 [`uhid`](#uhid),一次性配好键盘布局,一劳永逸。


## SDK 键盘

该模式(`--keyboard=sdk`,或省略参数)在 Android API 层面注入键盘输入事件。它到处可用,但仅支持 ASCII 和部分其他字符。

注意,某些设备上必须在开发者选项里额外开启一个选项,这种键盘模式才能工作。参见[前置条件](/README.md#prerequisites)。

下文还有若干专属于 `--keyboard=sdk` 的附加参数,可以自定义其行为。


### 文本注入偏好

输入文本时会生成两类[事件][textevents]:
 - _按键事件_:表示某个键被按下或释放;
 - _文本事件_:表示输入了一段文本。

默认情况下,数字和"特殊字符"用文本事件插入,而字母用按键事件注入,这样键盘在游戏里的表现才符合预期(典型如 WASD 键)。

但这可能[引发问题][prefertext]。如果遇到此类问题,可以把字母也按文本注入(或直接切换到 [UHID](#uhid)):

```bash
scrcpy --prefer-text
```

(但这会破坏键盘在游戏中的表现)

反过来,也可以强制总是注入原始按键事件:

```bash
scrcpy --raw-key-events
```

[textevents]: https://blog.rom1v.com/2018/03/introducing-scrcpy/#handle-text-input
[prefertext]: https://github.com/Genymobile/scrcpy/issues/650#issuecomment-512945343


### 按键重复

默认情况下,长按一个键会产生重复的按键事件。某些游戏里这些事件毫无用处,还会造成性能问题。

要停止转发重复按键事件:

```bash
scrcpy --no-key-repeat
```


## 物理键盘模拟

有两种模式可以在设备上模拟物理 HID 键盘。

要正常工作,必须(一次性)把设备上的键盘布局配置成与电脑一致。

可以通过以下任一方式打开配置页面:
 - 在 scrcpy 窗口中(使用 `uhid` 或 `aoa` 时)按 <kbd>MOD</kbd>+<kbd>k</kbd>(参见[快捷键](shortcuts.md))
 - 在设备上:设置 → 系统 → 语言和输入法 → 实体键盘
 - 在电脑终端里执行 `adb shell am start -a android.settings.HARD_KEYBOARD_SETTINGS`

在这个配置页里,还可以启用或禁用屏幕软键盘。


### UHID

该模式利用设备上的 [UHID] 内核模块模拟物理 HID 键盘。

[UHID]: https://kernel.org/doc/Documentation/hid/uhid.txt

启用 UHID 键盘:

```bash
scrcpy --keyboard=uhid
scrcpy -K  # 简写
```

键盘布局配置好之后(见上文),这是投屏时使用键盘的最佳模式:

 - 支持所有字符和输入法(与 `--keyboard=sdk` 相反)
 - 可以禁用屏幕软键盘(与 `--keyboard=sdk` 相反)
 - 可以通过 TCP/IP(无线)工作(与 `--keyboard=aoa` 相反)
 - 在 Windows 上没有问题(与 `--keyboard=aoa` 相反)

缺点是:在旧版 Android 上可能因权限错误而无法使用。


### AOA

该模式利用 [AOAv2] 协议模拟物理 HID 键盘。

[AOAv2]: https://source.android.com/devices/accessories/aoa2#hid-support

启用 AOA 键盘:

```bash
scrcpy --keyboard=aoa
```

与其他模式不同,它直接工作在 USB 层面(因此只能在 USB 连接下使用)。

它不使用 scrcpy server,也不需要 `adb`(USB 调试)。因此即使禁用 USB 调试,也可以控制设备(但不能投屏,参见 [OTG](otg.md))。

注意:在 Windows 上,它可能只在 [OTG 模式](otg.md)下可用,投屏时不可用(USB 设备已被其他进程——如 _adb 守护进程_——打开时,就无法再打开它)。
