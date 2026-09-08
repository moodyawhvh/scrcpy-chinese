> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 控制

## 只读模式

禁用一切控制手段(所有能与设备交互的东西:按键输入、鼠标事件、文件拖放):

```bash
scrcpy --no-control
scrcpy -n   # 简写
```

## 键盘和鼠标

参见 [键盘](keyboard.md) 和 [鼠标](mouse.md)。


## 仅控制

不投屏、只控制设备:

```bash
scrcpy --no-video --no-audio
```

默认情况下,视频播放关闭时鼠标也被禁用。

要用相对鼠标控制设备,启用 UHID 鼠标模式:

```bash
scrcpy --no-video --no-audio --mouse=uhid
scrcpy --no-video --no-audio -M  # 简写
```

还要用 UHID 键盘的话,需要显式设置:

```bash
scrcpy --no-video --no-audio --mouse=uhid --keyboard=uhid
scrcpy --no-video --no-audio -MK  # 简写
```

改用 AOA 的话(仅限 USB):

```bash
scrcpy --no-video --no-audio --keyboard=aoa --mouse=aoa
```


## 复制粘贴

Android 剪贴板每次变化,都会自动同步到电脑剪贴板。

所有 <kbd>Ctrl</kbd> 快捷键都会转发给设备,特别是:
 - <kbd>Ctrl</kbd>+<kbd>c</kbd> 通常执行复制
 - <kbd>Ctrl</kbd>+<kbd>x</kbd> 通常执行剪切
 - <kbd>Ctrl</kbd>+<kbd>v</kbd> 通常执行粘贴(先做电脑到设备的剪贴板同步)

一般表现和你的预期一致。

不过实际行为取决于当前活跃的应用。例如,_Termux_ 在 <kbd>Ctrl</kbd>+<kbd>c</kbd> 时发送的是 SIGINT 信号,而 _K-9 Mail_ 会新建一封邮件。

这种情况下想复制、剪切、粘贴(仅支持 Android >= 7):
 - <kbd>MOD</kbd>+<kbd>c</kbd> 注入 `COPY`
 - <kbd>MOD</kbd>+<kbd>x</kbd> 注入 `CUT`
 - <kbd>MOD</kbd>+<kbd>v</kbd> 注入 `PASTE`(先做电脑到设备的剪贴板同步)

另外,<kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>v</kbd> 会把电脑剪贴板文本作为一系列按键事件注入。这在组件不接受粘贴文本时很有用(比如 _Termux_),但可能破坏非 ASCII 内容。

**警告:** 把电脑剪贴板粘贴到设备(无论 <kbd>Ctrl</kbd>+<kbd>v</kbd> 还是 <kbd>MOD</kbd>+<kbd>v</kbd>)都会把内容复制进 Android 剪贴板。这样任何 Android 应用都能读到它。请避免用这种方式粘贴敏感内容(比如密码)。

部分 Android 设备在程序化设置设备剪贴板时表现异常。对此提供了 `--legacy-paste` 选项,改变 <kbd>Ctrl</kbd>+<kbd>v</kbd> 和 <kbd>MOD</kbd>+<kbd>v</kbd> 的行为,让它们同样把电脑剪贴板文本作为一系列按键事件注入(与 <kbd>MOD</kbd>+<kbd>Shift</kbd>+<kbd>v</kbd> 相同)。

要禁用剪贴板自动同步,使用 `--no-clipboard-autosync`。


## 双指缩放、旋转与倾斜模拟

模拟"双指缩放":<kbd>Ctrl</kbd>+_按住并移动_。

更准确地说,按住 <kbd>Ctrl</kbd> 的同时按下左键。在松开左键之前,所有鼠标移动都会以屏幕中心为基准缩放和旋转内容(如果应用支持)。

https://github.com/Genymobile/scrcpy/assets/543275/26c4a920-9805-43f1-8d4c-608752d04767

模拟垂直倾斜手势:<kbd>Shift</kbd>+_按住并上下移动_。

https://github.com/Genymobile/scrcpy/assets/543275/1e252341-4a90-4b29-9d11-9153b324669f

同理,模拟水平倾斜手势:<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+_按住并左右移动_。

原理上,_scrcpy_ 通过一个位置关于屏幕中心镜像的"虚拟手指"生成额外的触摸事件。按住 <kbd>Ctrl</kbd> 时,_x_ 和 _y_ 坐标都取反;只用 <kbd>Shift</kbd> 时只取反 _x_;而 <kbd>Ctrl</kbd>+<kbd>Shift</kbd> 只取反 _y_。

这只在默认鼠标模式(`--mouse=sdk`)下有效。


## 文件拖放

### 安装 APK

安装 APK:把一个 APK 文件(以 `.apk` 结尾)拖放到 _scrcpy_ 窗口即可。

没有可视化反馈,控制台会打印一条日志。


### 推送文件到设备

把(非 APK)文件拖放到 _scrcpy_ 窗口,即可推送到设备的 `/sdcard/Download/`。

没有可视化反馈,控制台会打印一条日志。

目标目录可以在启动时修改:

```bash
scrcpy --push-target=/sdcard/Movies/
```

每次推送成功后,_scrcpy_ 会请求媒体扫描器扫描"推送目录",让新文件立即出现在媒体应用中。

注意,部分相册应用的主界面只显示固定文件夹列表(通常是 `DCIM/Camera`)里的文件;此时文件仍可通过系统照片选择器和文件夹视图访问,但可能不会出现在相册首页。
