> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 鼠标

有几种鼠标输入模式可选:

 - `--mouse=sdk`(默认)
 - `--mouse=uhid`(或 `-M`):利用设备上的 UHID 内核模块模拟物理 HID 鼠标
 - `--mouse=aoa`:利用 AOAv2 协议模拟物理 HID 鼠标
 - `--mouse=disabled`


## SDK 鼠标

该模式(`--mouse=sdk`,或省略参数)在 Android API 层面以绝对坐标注入鼠标输入事件。

注意,某些设备上必须在开发者选项里额外开启一个选项,这种鼠标模式才能工作。参见[前置条件](/README.md#prerequisites)。

### 鼠标悬停

默认情况下,鼠标悬停事件(不点击的鼠标移动)会转发给设备。可以用以下命令禁用:

```bash
scrcpy --no-mouse-hover
```

## 物理鼠标模拟

有两种模式可以在设备上模拟物理 HID 鼠标。

这两种模式下,电脑鼠标会被"捕获":鼠标指针从电脑上消失,转而出现在 Android 设备上。

[快捷键修饰键](shortcuts.md)(默认为 <kbd>Alt</kbd> 或 <kbd>Super</kbd>)可以切换(禁用或启用)鼠标捕获。用它把鼠标控制权还给电脑。


### UHID

该模式利用设备上的 [UHID] 内核模块模拟物理 HID 鼠标。

[UHID]: https://kernel.org/doc/Documentation/hid/uhid.txt

启用 UHID 鼠标:

```bash
scrcpy --mouse=uhid
scrcpy -M  # 简写
```

注意:在旧版 Android 上,UHID 可能因权限错误而无法使用。


### AOA

该模式利用 [AOAv2] 协议模拟物理 HID 鼠标。

[AOAv2]: https://source.android.com/devices/accessories/aoa2#hid-support

启用 AOA 鼠标:

```bash
scrcpy --mouse=aoa
```

与其他模式不同,它直接工作在 USB 层面(因此只能在 USB 连接下使用)。

它不使用 scrcpy server,也不需要 `adb`(USB 调试)。因此即使禁用 USB 调试,也可以控制设备(但不能投屏,参见 [OTG](otg.md))。

注意:在 Windows 上,它可能只在 [OTG 模式](otg.md)下可用,投屏时不可用(USB 设备已被其他进程——如 _adb 守护进程_——打开时,就无法再打开它)。


## 鼠标按键绑定

默认情况下,SDK 鼠标模式下:
 - 右键触发 `BACK`(或点亮屏幕)
 - 中键触发 `HOME`
 - 第四键触发 `APP_SWITCH`
 - 第五键展开通知面板

按住 <kbd>Shift</kbd> 可以改为把这些次要点击转发给设备(例如 <kbd>Shift</kbd>+右键会把右键点击注入设备)。

在 AOA 和 UHID 鼠标模式下,默认绑定正好相反:所有点击默认转发,按住 <kbd>Shift</kbd> 才触发快捷键(这些模式下光标由设备侧处理,默认转发所有鼠标按键更合理)。

任何鼠标模式下,都可以用 `--mouse-bind=xxxx:xxxx` 配置快捷键。参数必须是 1~2 段(用 `:` 分隔)恰好 4 个字符的序列,每个字符对应一个次要点击:

```
                  .---- Shift + 右键
       SECONDARY  |.--- Shift + 中键
        BINDINGS  ||.-- Shift + 第四键
                  |||.- Shift + 第五键
                  ||||
                  vvvv
--mouse-bind=xxxx:xxxx
             ^^^^
             ||||
   PRIMARY   ||| `- 第五键
  BINDINGS   || `-- 第四键
             | `--- 中键
              `---- 右键
```

每个字符必须是以下之一:

 - `+`:把点击转发给设备
 - `-`:忽略该点击
 - `b`:触发快捷键 `BACK`(屏幕关闭时则点亮屏幕)
 - `h`:触发快捷键 `HOME`
 - `s`:触发快捷键 `APP_SWITCH`
 - `n`:触发快捷键"展开通知面板"

例如:

```bash
scrcpy --mouse-bind=bhsn:++++  # SDK 鼠标的默认模式
scrcpy --mouse-bind=++++:bhsn  # AOA 和 UHID 的默认模式
scrcpy --mouse-bind=++bh:++sn  # 右键和中键转发给设备,
                               # 第四键和第五键触发 BACK 和 HOME,
                               # Shift+第四键和 Shift+第五键触发 APP_SWITCH
                               # 和展开通知面板
```

第二段绑定可以省略,此时与第一段相同:

```bash
scrcpy --mouse-bind=bhsn
scrcpy --mouse-bind=bhsn:bhsn  # 等价
```
