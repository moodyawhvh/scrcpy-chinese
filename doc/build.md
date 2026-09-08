> 🌐 本文档由 [Genymobile/scrcpy](https://github.com/Genymobile/scrcpy) 翻译,英文原版见原项目。

# 编译 scrcpy

以下是编译 _scrcpy_(客户端和服务器)的说明。

如果你只是想编译并安装最新发行版,按照 [doc/linux.md](linux.md) 里的简化流程操作即可。

## 分支

有两个主要分支:
 - `master`:包含最新发行版,也是项目在 GitHub 上的主页。
 - `dev`:当前的开发分支。出现在 `dev` 中的每个提交都会进入下一个发行版。

如果你想贡献代码,请把你的提交基于最新的 `dev` 分支。


## 环境要求

你需要 [adb]。它可以在 [Android SDK platform tools][platform-tools] 中获取,也可以直接安装你发行版软件源里打包好的 `adb`。

在 Windows 上,下载 [platform-tools][platform-tools-windows],并把下面这些文件解压到一个位于 `PATH` 中的目录:
 - `adb.exe`
 - `AdbWinApi.dll`
 - `AdbWinUsbApi.dll`

scrcpy 的发行包里也自带了这些文件。

客户端需要 [FFmpeg] 和 [SDL],按照下文说明安装即可。

[adb]: https://developer.android.com/studio/command-line/adb.html
[platform-tools]: https://developer.android.com/studio/releases/platform-tools.html
[platform-tools-windows]: https://dl.google.com/android/repository/platform-tools-latest-windows.zip
[ffmpeg]: https://en.wikipedia.org/wiki/FFmpeg
[SDL]: https://en.wikipedia.org/wiki/Simple_DirectMedia_Layer



## 各系统专用步骤

### Linux

用包管理器安装所需的软件包。

#### Debian/Ubuntu

```bash
# runtime dependencies
sudo apt install ffmpeg libsdl3-0 adb libusb-1.0-0

# client build dependencies
sudo apt install gcc git pkg-config meson ninja-build libsdl3-dev \
                 libavcodec-dev libavdevice-dev libavformat-dev libavutil-dev \
                 libswresample-dev libusb-1.0-0-dev libv4l-dev

# server build dependencies
sudo apt install openjdk-17-jdk
```

在旧版本系统上(比如 Ubuntu 16.04),`meson` 版本太旧。这种情况下,用 `pip3` 安装:

```bash
sudo apt install python3-pip
pip3 install meson
```


#### Fedora

```bash
# enable RPM fusion free
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm

# client build dependencies
sudo dnf install SDL3-devel ffms2-devel libusb1-devel libavdevice-free-devel meson gcc make

# server build dependencies
sudo dnf install java-devel
```



### Windows

#### 在 Linux 上交叉编译

这是推荐做法(官方发行包就是这么构建的)。

在 _Debian_ 上安装 _mingw_:

```bash
sudo apt install mingw-w64 mingw-w64-tools libz-mingw-w64-dev
```

编译服务器还需要 JDK:

```bash
sudo apt install openjdk-17-jdk
```

然后生成发行包:

```bash
./release.sh
```

它会在 `dist/` 目录下生成 win32 和 win64 发行包。


#### 在 MSYS2 中

在 Windows 上,你需要 [MSYS2] 来构建本项目。在 MSYS2 终端里安装所需的包:

[MSYS2]: http://www.msys2.org/

```bash
# runtime dependencies
pacman -S mingw-w64-x86_64-sdl3 \
          mingw-w64-x86_64-ffmpeg \
          mingw-w64-x86_64-libusb

# client build dependencies
pacman -S mingw-w64-x86_64-make \
          mingw-w64-x86_64-gcc \
          mingw-w64-x86_64-pkg-config \
          mingw-w64-x86_64-meson
```

如果需要 32 位版本,把 `x86_64` 换成 `i686`:

```bash
# runtime dependencies
pacman -S mingw-w64-i686-sdl3 \
          mingw-w64-i686-ffmpeg \
          mingw-w64-i686-libusb

# client build dependencies
pacman -S mingw-w64-i686-make \
          mingw-w64-i686-gcc \
          mingw-w64-i686-pkg-config \
          mingw-w64-i686-meson
```

MSYS2 中没有 Java(>= 7),所以如果你打算编译服务器,需要手动安装 Java 并使其能从 `PATH` 中访问:

```bash
export PATH="$JAVA_HOME/bin:$PATH"
```

执行下文剩余的构建步骤时,请确保使用的是 MSYS2 中的 MinGW 终端。


### macOS

用 [Homebrew] 安装所需的包:

[Homebrew]: https://brew.sh/

```bash
# runtime dependencies
brew install sdl3 ffmpeg libusb

# client build dependencies
brew install pkg-config meson
```

另外,如果你想编译服务器,需要从 Caskroom 安装 Java 17,并使其能从 `PATH` 中访问:

```bash
brew install openjdk@17
export JAVA_HOME="$(/usr/libexec/java_home --version 1.17)"
export PATH="$JAVA_HOME/bin:$PATH"
```

### Docker

参见 [pierlon/scrcpy-docker](https://github.com/pierlon/scrcpy-docker)。


## 通用步骤

**以非 root 用户身份**克隆项目:

```bash
git clone https://github.com/Genymobile/scrcpy
cd scrcpy
```


### 编译

你可以选择只编译客户端:将被推送到 Android 设备上的服务器二进制文件并不依赖你的系统架构。这种情况下,可以使用[预编译服务器][prebuilt server](这样就不需要 Java 和 Android SDK 了)。

[prebuilt server]: #option-2-use-prebuilt-server


#### 方式一:从源码编译全部内容

安装 [Android SDK](_Android Studio_),并把 `ANDROID_SDK_ROOT` 设置为它的安装目录。例如:

[Android SDK]: https://developer.android.com/studio/index.html

```bash
# Linux
export ANDROID_SDK_ROOT=~/Android/Sdk
# Mac
export ANDROID_SDK_ROOT=~/Library/Android/sdk
# Windows
set ANDROID_SDK_ROOT=%LOCALAPPDATA%\Android\sdk
```

然后开始编译:

```bash
meson setup x --buildtype=release --strip -Db_lto=true
ninja -Cx  # DO NOT RUN AS ROOT
```

_注意:`ninja` [必须][ninja-user]以非 root 用户运行(只有 `ninja install` 需要以 root 运行)。_

[ninja-user]: https://github.com/Genymobile/scrcpy/commit/4c49b27e9f6be02b8e63b508b60535426bd0291a


#### 方式二:使用预编译服务器

 - [`scrcpy-server-v4.1`][direct-scrcpy-server]  
   <sub>SHA-256: `deacb991ed2509715160ffdc7907e47b4160eb30d1566217e9047fd5b8850cae`</sub>

[direct-scrcpy-server]: https://github.com/Genymobile/scrcpy/releases/download/v4.1/scrcpy-server-v4.1

把预编译服务器下载到任意位置,并在 Meson 配置时指定它的路径:

```bash
meson setup x --buildtype=release --strip -Db_lto=true \
    -Dprebuilt_server=/path/to/scrcpy-server
ninja -Cx  # DO NOT RUN AS ROOT
```

服务器只能与版本匹配的客户端一起工作(这个服务器对应 `master` 分支)。


### 不安装直接运行:

```bash
./run x [options]
```


### 安装

编译成功后,可以把 _scrcpy_ 安装到系统里:

```bash
sudo ninja -Cx install    # 在 Windows 上不需要 sudo
```

这一步会安装多个文件:

 - `/usr/local/bin/scrcpy`(主程序)
 - `/usr/local/share/scrcpy/scrcpy-server`(推送到设备使用的服务器)
 - `/usr/local/share/man/man1/scrcpy.1`(man 手册页)
 - `/usr/local/share/icons/hicolor/256x256/apps/scrcpy.png`(应用图标)
 - `/usr/local/share/icons/hicolor/256x256/apps/disconnected.png`(设备断开连接图标)
 - `/usr/local/share/zsh/site-functions/_scrcpy`(zsh 补全)
 - `/usr/local/share/bash-completion/completions/scrcpy`(bash 补全)
 - `/usr/local/share/applications/scrcpy.desktop`(无控制台启动的桌面入口文件)
 - `/usr/local/share/applications/scrcpy-console.desktop`(带控制台启动的桌面入口文件)

之后就可以直接运行 `scrcpy` 了。


### 卸载

```bash
sudo ninja -Cx uninstall  # 在 Windows 上不需要 sudo
```
