# 安装

## 命令行工具

1. 建立Android SDK目录，例如d:\android\sdk
2. 在android官网下载命令行工具最新版: 在[官方页面](https://developer.android.com/studio)页面选择下载"Command line tools only"
3. 解压到任意目录，例如d:\temp
4. 在解压后的bin目录下执行命令，在Android SDK目录安装命令行工具最新版
``` bash
sdkmanager --sdk_root=d:\android\sdk "cmdline-tools;latest"
```
5. 设置'ANDROID_SDK_ROOT'为d:\android\sdk
6. 在'PATH'环境变量中增加%ANDROID_SDK_ROOT%\cmdline-tools\latest\bin
7. 删除d:\temp下解压文件

## 安装SDK和平台工具
1. 使用sdkmanager安装
``` bash
sdkmanager "platform-tools" "platforms;android-30"
```
2. 在'PATH'环境变量中增加%ANDROID_SDK_ROOT%\platform-tools

## 安装构建工具
``` bash
sdkmanager "platform-tools" "build-tools;30.0.2"
```

## 安装模拟器 
1. 使用sdkmanager安装
``` bash
sdkmanager emulator
```

2. 安装镜像
``` bash
sdkmanager " system-images;android-30;google_apis_playstore;x86_64"
```

3. 在'PATH'环境变量中增加%ANDROID_SDK_ROOT%\emulator

# 使用模拟器

## 创建模拟器实例
例子：
``` bash
avdmanager create avd -n testAvd -k "system-images;android-25;google_apis;x86" -p d:\avd
```
-n 模拟器实例名称
-k 镜像名称
-p 模拟器实例文件保存路径


# FAQ

## Could not create settings错误信息解决方法

问题:

执行sdkmanager总是提示如下内容 
```
Warning: Could not create settings
java.lang.IllegalArgumentException
        at com.android.sdklib.tool.sdkmanager.SdkManagerCliSettings.<init>(SdkManagerCliSettings.java:428)
        at com.android.sdklib.tool.sdkmanager.SdkManagerCliSettings.createSettings(SdkManagerCliSettings.java:152)
        at com.android.sdklib.tool.sdkmanager.SdkManagerCliSettings.createSettings(SdkManagerCliSettings.java:134)
        at com.android.sdklib.tool.sdkmanager.SdkManagerCli.main(SdkManagerCli.java:57)
        at com.android.sdklib.tool.sdkmanager.SdkManagerCli.main(SdkManagerCli.java:48)
```

解决方案：
参见前面的"安装命令行工具"的方法安装命令行工具，不要直接使用官方下载的命令行工具包解压。

# 参考

[官网命令行工具帮助](https://developer.android.google.cn/studio/command-line)

