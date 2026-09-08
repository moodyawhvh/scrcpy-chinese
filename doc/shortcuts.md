> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 快捷键

在 scrcpy 窗口内,可以用键盘和鼠标快捷键执行各种操作。

在下面的列表中,<kbd>MOD</kbd> 是快捷键修饰键。默认为(左)<kbd>Alt</kbd> 或(左)<kbd>Super</kbd>。

可以用 `--shortcut-mod` 修改。可选按键有 `lctrl`、`rctrl`、`lalt`、`ralt`、`lsuper` 和 `rsuper`。例如:

```bash
# use RCtrl for shortcuts
scrcpy --shortcut-mod=rctrl

# use either LCtrl or LSuper for shortcuts
scrcpy --shortcut-mod=lctrl,lsuper
```

_<kbd>[Super]</kbd> 通常指 <kbd>Windows</kbd> 键或 <kbd>Cmd</kbd> 键。_

[Super]: https://en.wikipedia.org/wiki/Super_key_(keyboard_button)

 | 操作                                        |   快捷键
 | ------------------------------------------- |:-----------------------------
 | 退出                                        | <kbd>MOD</kbd>+<kbd>q</kbd>
 | 切换全屏                                    | <kbd>MOD</kbd>+<kbd>f</kbd> \| <kbd>F11</kbd>
 | 显示向左旋转                                | <kbd>MOD</kbd>+<kbd>←</kbd> _(左)_
 | 显示向右旋转                                | <kbd>MOD</kbd>+<kbd>→</kbd> _(右)_
 | 显示水平翻转                                | <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>←</kbd> _(左)_ \| <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>→</kbd> _(右)_
 | 显示垂直翻转                                | <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>↑</kbd> _(上)_ \| <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>↓</kbd> _(下)_
 | 暂停显示(或再次暂停)                       | <kbd>MOD</kbd>+<kbd>z</kbd>
 | 取消暂停显示                                | <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>z</kbd>
 | 重置视频采集/编码                           | <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>r</kbd>
 | 窗口调整为 1:1(像素级精确)                | <kbd>MOD</kbd>+<kbd>g</kbd>
 | 窗口调整为去除黑边                          | <kbd>MOD</kbd>+<kbd>w</kbd> \| _左键双击¹_
 | 点击 `HOME`                                 | <kbd>MOD</kbd>+<kbd>h</kbd> \| _鼠标中键_
 | 点击 `BACK`                                 | <kbd>MOD</kbd>+<kbd>b</kbd> \| <kbd>MOD</kbd>+<kbd>Backspace</kbd> \| _鼠标右键²_
 | 点击 `APP_SWITCH`                           | <kbd>MOD</kbd>+<kbd>s</kbd> \| _第四键³_
 | 点击 `MENU`(解锁屏幕)⁴                     | <kbd>MOD</kbd>+<kbd>m</kbd>
 | 点击 `VOLUME_UP`                            | <kbd>MOD</kbd>+<kbd>↑</kbd> _(上)_
 | 点击 `VOLUME_DOWN`                          | <kbd>MOD</kbd>+<kbd>↓</kbd> _(下)_
 | 点击 `POWER`                                | <kbd>MOD</kbd>+<kbd>p</kbd>
 | 点亮屏幕                                    | _鼠标右键²_
 | 关闭设备屏幕(继续投屏)                    | <kbd>MOD</kbd>+<kbd>o</kbd>
 | 打开设备屏幕                                | <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>o</kbd>
 | 旋转设备屏幕                                | <kbd>MOD</kbd>+<kbd>r</kbd>
 | 展开通知面板                                | <kbd>MOD</kbd>+<kbd>n</kbd> \| _第五键³_
 | 展开设置面板                                | <kbd>MOD</kbd>+<kbd>n</kbd>+<kbd>n</kbd> \| _第五键双击³_
 | 收起面板                                    | <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>n</kbd>
 | 复制到剪贴板⁵                               | <kbd>MOD</kbd>+<kbd>c</kbd>
 | 剪切到剪贴板⁵                               | <kbd>MOD</kbd>+<kbd>x</kbd>
 | 同步剪贴板并粘贴⁵                           | <kbd>MOD</kbd>+<kbd>v</kbd>
 | 注入电脑剪贴板文本                          | <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>v</kbd>
 | 打开键盘设置(仅 HID 键盘)                 | <kbd>MOD</kbd>+<kbd>k</kbd>
 | 启用/停用 FPS 计数器(输出到 stdout)      | <kbd>MOD</kbd>+<kbd>i</kbd>
 | 双指缩放/旋转                               | <kbd>Ctrl</kbd>+_按住并移动_
 | 垂直倾斜(双指滑动)                        | <kbd>Shift</kbd>+_按住并移动_
 | 水平倾斜(双指滑动)                        | <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+_按住并移动_
 | 拖放 APK 文件                               | 从电脑安装 APK
 | 拖放非 APK 文件                             | [推送文件到设备](control.md#push-file-to-device)
 | 打开相机手电筒(仅相机模式)                | <kbd>MOD</kbd>+<kbd>t</kbd>
 | 关闭相机手电筒(仅相机模式)                | <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>t</kbd>
 | 相机变焦放大(仅相机模式)                  | <kbd>MOD</kbd>+<kbd>↑</kbd> _(上)_
 | 相机变焦缩小(仅相机模式)                  | <kbd>MOD</kbd>+<kbd>↓</kbd> _(下)_

_¹双击黑边即可去除黑边。_  
_²屏幕关闭时,右键点亮屏幕;否则右键相当于 BACK。_  
_³指鼠标第四、第五侧键(如果你的鼠标有的话)。_  
_⁴对开发中的 react-native 应用,`MENU` 会触发开发者菜单。_  
_⁵仅支持 Android >= 7。_

带重复按键的快捷键通过"松开后再次按下"来触发。例如,要执行"展开设置面板":

 1. 按住 <kbd>MOD</kbd> 不放。
 2. 然后连按两次 <kbd>n</kbd>。
 3. 最后松开 <kbd>MOD</kbd>。

所有 <kbd>Ctrl</kbd>+_按键_ 组合都会被转发给设备,由设备上当前活跃的应用处理。
