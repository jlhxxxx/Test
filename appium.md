# Appium环境搭建及自动化的一些参考

## Appium环境配置

**本地环境**：win10_x64 + JDK8 + python3

### Appium-Client安装配置

这里我们选用[python-client](https://github.com/appium/python-client)，建议在虚拟环境下安装，命令如下：

```
:::python
pip install Appium-Python-Client
```

### Appium-Server安装配置

推荐使用[appium-desktop](https://github.com/appium/appium-desktop)，图形化界面，自带Inspector，非常好用。

### Android SDK安装配置

#### 设置SDK环境变量

这里只讲不使用Android Studio情况下安装SDK，首先新建一个SDK主目录（空文件夹即可），假设是`D:\SDK`。配置相应环境变量：

| 变量名       | 变量值                                               |
| ------------ | ---------------------------------------------------- |
| ANDROID_HOME | D:\SDK                                               |
| PATH         | ;%ANDROID_HOME%\platform-tools;%ANDROID_HOME%\tools; |

#### 下载SDK tools并安装其他工具

官方地址：<https://developer.android.com/studio>。拉到最下面**Command line tools only**处，下载Windows版本，目前版本为：`sdk-tools-windows-4333796.zip`。

> 有必要说明一下，支持图形化界面的SDK manager和AVD manager的SDK tools最高版本为25.2.5，只支持Android-25（即Android 7.1.1）及以下版本的虚拟机运行。
>
> 最新版本的SDK tools（目前为26.1.1）只支持纯命令行运行SDK manager和AVD manager。可以运行更高版本的虚拟机。

**安装其他工具有两种方法**

- **（新手推荐）通过第三方图形化界面工具[B4A](https://www.b4x.com/b4a.html#installation)**：安装好B4A后配置javac.exe和sdkmanager.bat路径，即可使用图形化界面的SDK manager和AVD manager。
- **（需对SDK工具有较深的理解）通过[SDKmanager](https://developer.android.com/studio/command-line/sdkmanager)下载**：将上面下载的SDK tools解压到SDK主目录下，进入`tools\bin\`目录，找到sdkmanager.bat，在命令提示符下输入`sdkmanager --list`，列出可以安装的工具，输入`sdkmanager <package>`安装相应工具（其中`packages`参数为软件包的`Path`名称，需带引号）；使用`sdkmanager --update`命令更新已安装的工具（如果有更新的版本）。

要运行相应的虚拟机，需要安装对应版本的SDK Platform和System Image以及相应或更高版本的SDK Buid-Tools。例如，如果我要运行Android 9（Android-28）虚拟机，需要安装SDK Platform 28，由于我的CPU支持Intel HAXM且是64位，所以System Image选择Intel x86 Atom_64 System Image（Android-28），SDK Buid-Tools选择最新版本Android SDK Buid-Tools 29，其他工具可以按B4A推荐的安装。

> 关于**Intel HAXM**，大致就是使用基于Intel(R) Virtualization Technology (VT) 的硬件加速，提高Android模拟器的响应速度。下载后需要单独安装。官方GITHUB地址：<https://github.com/intel/haxm>

#### 配置并运行虚拟机

按新手推荐下载安装相应包之后，运行B4A AVD Manager，选择好屏幕尺寸和Platform，创建AVD后即可运行虚拟机。这里可配置的选项只有2个，但是没有关系，我们后面可以直接修改配置文件。

这里重点讲一下虚拟机配置文件，理解了之后就可以即不通过图形化界面也不通过命令行手动创建虚拟机。

AVD配置文件主目录在`C:\Users\xxx\.android\avd`，其中`xxx`为计算机名称，如果是通过Android AVD manager创建的虚拟机，在该目录下会有对应的一个虚拟机存放目录和一个目录配置文件，假设虚拟机名称为ad9，则有虚拟机存放目录`ad9\`和配置文件`ad9.ini`；如果是通过b4a AVD manager创建的虚拟机，虚拟机存放目录会被转移到`SDK\B4AEmulator\`目录下，打开ini配置文件，内容大概有3行：

```
avd.ini.encoding=UTF-8
path=D:\SDK\B4AEmulator\ad9
target=android-28
```

其中`path`配置的就是虚拟机存放目录。

再进入虚拟机存放目录下，会有一个`config.ini`，这就是虚拟机的配置文件，下面以我的配置文件来说明一下：

```
avd.ini.encoding=UTF-8									# 配置文件编码
PlayStore.enabled=false									# 是否使用Google PlayStore
abi.type=x86_64											# abi类型，见下文
hw.accelerometer=yes									# 加速度计
hw.audioInput=yes										# 音频
hw.battery=yes											# 电池
hw.camera.back=none										# 后置摄像头
hw.camera.front=none									# 前置摄像头
hw.cpu.arch=x86_64										# cpu架构
hw.dPad=no												# 对应模拟器DPAD按键
hw.device.hash2=MD5:fbd5143f5b48ba972391c87c302c0c69	# 设备hash值，可以不用
hw.device.manufacturer=Generic							# 设备厂家
hw.device.name=Jackson									# 设备名称
hw.gps=yes												# GPS
hw.gpu.enabled=yes										# GPU加速，见下文
hw.gpu.mode=auto
hw.keyboard=yes											# 是否启用windows键盘支持
hw.lcd.density=160										# lcd像素密度
hw.lcd.height=800
hw.lcd.width=480
hw.mainKeys=yes											# 是否不显示底部导航栏
hw.sdCard=no											# 是否使用sdCard，可以配置大小
hw.sensors.orientation=yes								# 方向传感器
hw.sensors.proximity=yes								# 距离传感器
hw.trackBall=no											# 轨迹球
image.sysdir.1=system-images\android-28\default\x86_64\ # 系统映像目录
skin.dynamic=no											# 系统皮肤（带按键的）
skin.name=480x800
skin.path=480x800
tag.display=Default										# 标签显示
tag.id=default
```

- `abi.type`：abi即Application Binary Interface，配置应与`cpu.arch`对应，参考[ABI 管理](https://developer.android.com/ndk/guides/abis)。
- `hw.gpu.enabled`和`hw.gpu.mode`，参考[Configure hardware acceleration for the Android Emulator](https://developer.android.com/studio/run/emulator-acceleration)。

两个配置文件掌握之后，我们就可以很方便地通过配置文件来创建虚拟机。其实只需要创建虚拟机存放目录和这2个配置文件，虚拟机就算建好了，至于虚拟机存放目录下的其他文件，会在第一次启动虚拟机后自动生成。

**通过命令行启动虚拟机**

虚拟机创建好后，可以进入`SDK\emulator\`目录下，在命令提示符下输入`emulator @name`命令即可启动虚拟机，其中`name`为虚拟机名称。为了更方便启用，可以将`%ANDROID_HOME%\emulator;`添加到Path环境变量，但是要注意放到`%ANDROID_HOME%\tools;`之前，因为tools目录下也有emulator.exe，用它启动虚拟机会报错。

启动的附加命令，请参考[Start the emulator from the command line](https://developer.android.com/studio/run/emulator-commandline)

## Appium自动化相关

#### 查询apk包信息

使用`aapt`（位于`sdk\build-tools\`下）:

```
aapt.exe dump badging D:\xxx\xxx.apk
```

其中：

```
包名：  package: name=’xxx’ 
入口类：launchable-activity: name=’xxx’ 
```

#### 常用Inspect工具

1. **Appium Desktop Inspector（推荐）**：稳定且好用，还有录制功能。需要配置Desired Capabilities，具体使用参考[Appium Desktop官方文档](https://github.com/appium/appium-desktop#the-inspector)。

2. **uiautomatorviewer.bat**：在`tools\bin\`目录下，如果连接报错，通常需要重启adb：

    ```
    adb kill-server
    adb start-server
    ```

3. **Chrome inspector**：只能解析webview元素，且需要翻墙，逼格很高。

#### 无法定位到元素的问题

如果定位不到**native**元素，可以尝试在Desired Capabilities增加一行：

```
:::python
desired_caps['automationName'] = "uiautomator2"
```

如果定位不到**webview**元素，可以通过`switch_to.context`方法，类似Selenium的`switch_to.frame`：

```
:::python
>>> contexts = driver.contexts
>>> print contexts
['NATIVE_APP', 'WEBVIEW_org.chromium.webview_shell', 'WEBVIEW_mark.via']
>>> driver.switch_to.context(contexts[1])
```

如果要返回定位native元素，记得切换回来：

```
:::python
>>> driver.switch_to.context('NATIVE_APP')
```

#### 切换webview报Chrome version错误

官方文档[Chromedriver](http://appium.io/docs/cn/advanced-concepts/chromedriver/)给出了解释：

> 每次 Chromedriver 升级，支持的 Chrome 的最小版本都会升级，这就导致了老设备上经常无法在绑定的版本上自动化。

查看Appium Desktop日志，到官方文档上下载相应版本的Chromedriver并替换最新版即可。

### 参考文章：

- 简洁明晰版本：[浅谈自动化测试工具 Appium](https://testerhome.com/topics/11318)
- 较详细版本：[Appium 简明教程](http://www.testclass.net/appium)
- 其他参考
    - [GUI SDK Manager for newer versions of Android tools](https://www.b4x.com/android/forum/threads/gui-sdk-manager-for-newer-versions-of-android-tools.80090/)
    - [UI Automator Viewer工具的使用](https://www.cnblogs.com/lhx0827/p/9577575.html)
    - [混合开发使用Chrome Inspect调试WebView预览手机界面和定位元素](https://www.cnblogs.com/hjblog/p/9055105.html)
    - [Appium+python自动化13-native和webview切换](https://blog.csdn.net/weixin_39142498/article/details/79991380)
    - [appium+python自动化41-切换webview时候报chromedriver版本问题](https://www.cnblogs.com/yoyoketang/p/9009209.html)