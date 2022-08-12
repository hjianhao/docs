# MacOS FAQ

## 目录

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [MacOS FAQ](#macos-faq)
  - [目录](#目录)
  - [网络](#网络)
    - [为什么总弹出“连接服务器时出现问题”](#为什么总弹出连接服务器时出现问题)
  - [常用操作](#常用操作)
    - [截图](#截图)
    - [切换桌面](#切换桌面)

<!-- /code_chunk_output -->

## 网络

### 为什么总弹出“连接服务器时出现问题”

我有台NAS换了IP地址后，总会弹出“连接服务器时出现问题”，我删除了所有相关的连接内容也一样。

解决办法： 打开"系统偏好设置/用户和组”，在此处选择您的用户名，然后单击“登录项”。检查此列表中是否有网络驱动器，例如NAS上的共享。如果是，则表示您的Mac在启动计算机时会自动尝试登录该驱动器。删除掉登录项就好了。

## 常用操作

### 截图

（+表示同时操作、-表示分步操作）

基础快捷键

Cmd+Shift+3：全屏截图；截取整个屏幕，保存截图至桌面文件夹。

Cmd+Shift+4：区域截图；鼠标光标变成带坐标的小十字，通过拖拽截取特定区域，保存截图至桌面文件夹。

Cmd+Shift+4 - 单击空格键 - 鼠标单击指定窗口：窗口截图；出现小十字光标后对指定窗口按空格键，鼠标光标变成照相机，鼠标单击截取指定窗口，保存截图至桌面文件夹。

进阶快捷键

Cmd+Shift+4 - 按住空格键拖动鼠标：区域截图；选取区域范围后，按住空格键并拖到鼠标可移动选取范围，释放按键后保存截图至桌面文件夹。

Cmd+Shift+4 - 按住Shift - 上下/左右移动鼠标：区域截图；选取区域范围后，按住 Shift 并上下/左右移动鼠标即可固定选取范围宽度/高度改变高度/宽度，释放按键后保存截图至桌面文件夹。

Cmd+Shift+4 - 按住Shift和Option: 区域截图；选取区域范围后，按住 Shift 和 Option 键在上一快捷键功能的基础上自由切换高度宽度，释放按键后保存截图至桌面文件夹。

Cmd+Shift+4 - 按住Option: 区域截图；选取区域范围后，按住 Option 键可按比例缩放选取范围，释放按键后保存截图至桌面文件夹。

PS：以上介绍的快捷键配合按住 Control 键即可将截图文件保存在剪切板，以供调用。

利用终端截图还有比较 Geek 的方法就是通过终端（Terminal）命令来截图，终端截图命令screencapture 有十多种参数可以选择，比如截图后立即显示、消除截图声音、延迟数秒后截图等。有兴趣的读者可以通过下面这条命令查看各个参数的具体使用方法。（运行结果即本文配图）screencapture -h系统自带截屏的技巧都在这里了，


### 切换桌面

^ Ctrl键
^1 ^2 ^3 分别切换到三个桌面，快捷键可以定制。
参考下图：
![按键设置](images/faq/key-settings.png)
