> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 设备

scrcpy 运行期间,一些命令行参数可以直接对设备本身执行操作。


## 保持活跃

`--keep-active` 会周期性地向系统上报用户活动,防止设备因无操作而熄屏:

```bash
scrcpy --keep-active
```


## 保持唤醒

防止设备**在接通电源的情况下**延时后进入休眠:

```bash
scrcpy --stay-awake
scrcpy -w
```

_scrcpy_ 关闭时会恢复初始状态。

如果设备没有插电(即仅通过 TCP/IP 连接),`--stay-awake` 无效(这是 Android 本身的行为)。

它修改的是 [`stay_on_while_plugged_in`] 设置项,这项设置也可以手动修改:

[`stay_on_while_plugged_in`]: https://developer.android.com/reference/android/provider/Settings.Global#STAY_ON_WHILE_PLUGGED_IN


```bash
# get the current stay_on_while_plugged_in value
adb shell settings get global stay_on_while_plugged_in
# enable for AC/USB/wireless chargers
adb shell settings put global stay_on_while_plugged_in 7
# disable
adb shell settings put global stay_on_while_plugged_in 0
```


## 熄屏超时

Android 屏幕会在一段延时后自动熄灭。

scrcpy 运行期间可以修改这个延时:

```bash
scrcpy --screen-off-timeout=300  # 300 秒(5 分钟)
```

退出时恢复初始值。

这项设置也可以手动修改:

```bash
# get the current screen_off_timeout value
adb shell settings get system screen_off_timeout
# set a new value (in milliseconds)
adb shell settings put system screen_off_timeout 30000
```

注意:Android 侧的值以毫秒为单位,而 scrcpy 命令行参数以秒为单位。


## 关闭屏幕

可以在启动投屏的同时用命令行选项关闭设备屏幕:

```bash
scrcpy --turn-screen-off
scrcpy -S   # 简写
```

也可以随时按 <kbd>MOD</kbd>+<kbd>o</kbd>(参见[快捷键](shortcuts.md))。

重新点亮屏幕:按 <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>o</kbd>。

在 Android 上,`POWER` 键总是点亮屏幕。为了方便,如果 `POWER` 是通过 _scrcpy_ 发送的(右键或 <kbd>MOD</kbd>+<kbd>p</kbd>),scrcpy 会在短暂延时后尽力把屏幕再关掉。物理 `POWER` 按键仍会正常点亮屏幕。

配合防止休眠会更有用:

```bash
scrcpy --turn-screen-off --stay-awake
scrcpy -Sw   # 简写
```

从 Android 15 开始,这项设置也可以手动修改:

```bash
# turn screen off (0 for main display)
adb shell cmd display power-off 0
# turn screen on
adb shell cmd display power-on 0
```


## 显示触摸

做演示时,显示(物理设备上的)真实触摸操作可能很有用。Android 在_开发者选项_里提供了这个功能。

_Scrcpy_ 提供了一个选项,可在启动时开启此功能,并在退出时恢复初始值:

```bash
scrcpy --show-touches
scrcpy -t   # 简写
```

注意,它只显示_物理_触摸(手指在设备上的触摸)。

这项设置也可以手动修改:

```bash
# get the current show_touches value
adb shell settings get system show_touches
# enable show_touches
adb shell settings put system show_touches 1
# disable show_touches
adb shell settings put system show_touches 0
```

## 关闭时熄屏

关闭 _scrcpy_ 时熄灭设备屏幕:

```bash
scrcpy --power-off-on-close
```

## 启动时点亮屏幕

默认情况下,启动时会点亮设备。要禁用这个行为:

```bash
scrcpy --no-power-on
```


## 启动 Android 应用

列出设备上安装的 Android 应用:

```bash
scrcpy --list-apps
```

可以在启动时运行某个应用,用包名指定:

```bash
scrcpy --start-app=org.mozilla.firefox
```

这个功能可以配合[虚拟显示器](virtual-display.md)运行应用:

```bash
scrcpy --new-display=1920x1080 --start-app=org.videolan.vlc
```

在包名前加 `+` 前缀,可以在启动前强制停止该应用:

```bash
scrcpy --start-app=+org.mozilla.firefox
```

为了方便,也可以加 `?` 前缀按应用名称选择:

```bash
scrcpy --start-app=?firefox
```

但获取应用名称可能耗时(有时要好几秒),因此建议直接传包名。

`+` 和 `?` 前缀可以组合使用(按此顺序):

```bash
scrcpy --start-app=+?firefox
```
