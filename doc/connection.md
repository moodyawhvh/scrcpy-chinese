> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 连接

## 设备选择

如果只连接了一台设备(即 `adb devices` 只列出一台),它会自动被选中。

但如果连接了多台设备,你必须用以下 4 种方式之一指定要使用的设备:
 - 通过序列号:
   ```bash
   scrcpy --serial=0123456789abcdef
   scrcpy -s 0123456789abcdef   # 简写

   # 如果通过 TCP/IP 连接,序列号就是 ip:port(与 adb 行为一致)
   scrcpy --serial=192.168.1.1:5555
   ```
 - 通过 USB 连接的那台(如果恰好只有一台):
   ```bash
   scrcpy --select-usb
   scrcpy -d   # 简写
   ```
 - 通过 TCP/IP 连接的那台(如果恰好只有一台):
   ```bash
   scrcpy --select-tcpip
   scrcpy -e   # 简写
   ```
 - 一台已经在 TCP/IP 上监听的设备(见[下文](#tcpip无线)):
   ```bash
   scrcpy --tcpip=192.168.1.1:5555
   scrcpy --tcpip=192.168.1.1        # 默认端口是 5555
   ```

序列号也可以通过环境变量 `ANDROID_SERIAL` 提供(`adb` 同样识别这个变量):

```bash
# 在 bash 中
export ANDROID_SERIAL=0123456789abcdef
scrcpy
```

```cmd
:: 在 cmd 中
set ANDROID_SERIAL=0123456789abcdef
scrcpy
```

```powershell
# 在 PowerShell 中
$env:ANDROID_SERIAL = '0123456789abcdef'
scrcpy
```


## TCP/IP(无线)

_Scrcpy_ 通过 `adb` 与设备通信,而 `adb` 可以通过 TCP/IP [连接][connect]设备。设备必须与电脑处于同一网络中。

[connect]: https://developer.android.com/studio/command-line/adb.html#wireless


### 自动方式

`--tcpip` 选项可以自动完成连接配置,有两种用法。

如果设备上的 _adb_ TCP/IP 模式未开启(或者你不知道设备的 IP 地址),先用 USB 连接设备,然后运行:

```bash
scrcpy --tcpip   # 不带参数
```

它会自动找到设备的 IP 地址和 adb 端口,必要时开启 TCP/IP 模式,然后在启动前连接到设备。

如果设备(本例中为 192.168.1.1)已经在某个端口(通常是 5555)上监听 _adb_ 连接,则直接运行:

```bash
scrcpy --tcpip=192.168.1.1       # 默认端口是 5555
scrcpy --tcpip=192.168.1.1:5555
```

在地址前加一个 `+` 可以强制重新连接:

```bash
scrcpy --tcpip=+192.168.1.1
```


### 手动方式

也可以用 `adb` 手动开启 TCP/IP 连接:

1. 把设备插到电脑的 USB 口上。
2. 让设备与电脑连接同一个 Wi-Fi 网络。
3. 获取设备 IP 地址:在 设置 → 关于手机 → 状态信息 中查看,或执行以下命令:

    ```bash
    adb shell ip route | awk '{print $9}'
    ```

4. 在设备上开启 `adb` 的 TCP/IP 模式:`adb tcpip 5555`。
5. 拔掉设备。
6. 连接设备:`adb connect 设备IP:5555` _(把 `设备IP` 换成你查到的设备 IP 地址)_。
7. 像平常一样运行 `scrcpy`。
8. 用完之后运行 `adb disconnect`。

从 Android 11 开始,[无线调试选项][adb-wireless]可以让你不必把设备物理连接到电脑。

[adb-wireless]: https://developer.android.com/studio/command-line/adb#wireless-android11-command-line


## 自动启动

一个小工具(scrcpy 作者出品)可以在检测到新的 Android 设备接入时运行任意命令:[AutoAdb]。可以用它来启动 scrcpy:

```bash
autoadb scrcpy -s '{}'
```

[AutoAdb]: https://github.com/rom1v/autoadb
