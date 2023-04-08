---
title: 搭建一个用于工作和学习的Linux桌面环境的尝试
zhihu-url: https://zhuanlan.zhihu.com/p/449344466
zhihu-tags: Linux, Ubuntu, Desktop
---

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->
- [概述](#概述)
- [折腾过程](#折腾过程)
  - [操作系统](#操作系统)
    - [Ubuntu下载与引导](#ubuntu下载与引导)
    - [分区与引导](#分区与引导)
    - [复制EFI到移动硬盘](#复制efi到移动硬盘)
    - [显卡驱动](#显卡驱动)
    - [多显示器](#多显示器)
  - [基础软件](#基础软件)
    - [输入法](#输入法)
    - [KeePass](#keepass)
    - [词典](#词典)
  - [开发环境](#开发环境)
    - [安装vscode](#安装vscode)
    - [Java开发环境](#java开发环境)
      - [JDK安装](#jdk安装)
      - [Maven安装](#maven安装)
      - [安装vscode插件](#安装vscode插件)
      - [安装IDEA](#安装idea)
    - [C++开发环境](#c开发环境)
    - [Markdown](#markdown)
  - [音乐播放](#音乐播放)
    - [流媒体](#流媒体)
    - [本地音乐](#本地音乐)
      - [挂接NAS](#挂接nas)
      - [本地播放器选择](#本地播放器选择)
        - [安装mpd](#安装mpd)
        - [配置mpd](#配置mpd)
        - [使用mpd](#使用mpd)
  - [社交](#社交)
    - [微信与QQ](#微信与qq)

<!-- /code_chunk_output -->

# 概述

作为一个长期的Windows和macOS桌面用户，我基本没有接触过Linux的桌面环境，但是工作和学习中长期使用Linux作为服务器操作系统部署生产和测试环境。突发奇想，想看看现在的Linux的桌面环境是否可以作为n工作和学习环境，于是我就试着搭建了一下。

先说一下我对工作学习环境的基本定义：
1. 能使用日常开发所需的IDE（基本开发环境反而不担心，毕竟主要开发测试部署的环境都是Linux）
2. 能听听音乐
3. 能进行轻量办公（其实很反感写各种花里胡哨的文档，除了汇报和需要按模版写的文档外，主要的文档写作主要是Markdown），就是要有基本的Office功能
4. 要有基本社交功能，还是经常有交流需求的。
5. 安装在移动固态硬盘（PSSD）上，可以在不同的地方直接插上使用无缝衔接，需要带一个编写的移动硬盘。

初步印象：
1. 如果是为了基本开发，感觉Linux当前桌面环境也够了，但是Windows+WSL不是更香吗？真正生产和测试环境部署也是到Linux服务器上运行啊。
2. Linux桌面生态不太行。很多软件都没有，大概是凑合着用。

# 折腾过程

## 操作系统

操作系统选择的是Ubuntu 20.04，应该区别不大，也算是最流行的Desktop发行版之一。

我将操作系统安装在PSSD，同时要能从PSSD启动。

### Ubuntu下载与引导

操作系统安装ISO在如下地址下载： https://ubuntu.com/#download

然后找一个U盘，通过官方推荐的Rufus将ISO写入U盘制作Live引导盘

重启后用U盘启动进入Ubuntu Live。然后点击安装系统的图标安装。 最好不要选择启动菜单的oem install，因为这个安装完后还有一个OEM迁移到普通用户的过程。

### 分区与引导
可以其他用DiskGenius在安装前分区，或者在安装的过程中选择手动分区。（在选择安全步骤选项选择其他选项）

为了能将安装了Ubuntu系统的PSSD在多台电脑上启动，所以需要给一个EFI分区

| 分区 | 大小 | 用途 | 类型 | 是否主分区 |
| -- | -- | -- | -- | -- |
| EFI | 1G | UEFI启动 | EFI | Y |
| swap | 64G(注1) | 内存交换 | swap | N |
| / | 256G | 系统盘 | ext4 | N |
| /home | 500G | 用户空间 | ext4 | N |
| U盘 | 128G | 用于Linux和<br>Windows交换数据<br>顺便当U盘(注2) | exFat | Y |

注1 ： swap分区一般和物理内存大小相等，这里分64G是我各处电脑最大的内存
注2 ： 无论是Windows读写ext4还是Linux读写NTFS都不是那么方便稳定，一个exFat分区用于交换数据皆大欢喜。

总体来说，分区比较简单，将"/"和"/home"是为了方便重装系统，其他的从简。

### 复制EFI到移动硬盘

安装完成后，移动硬盘的EFI分区是空的，多系统启动是使用电脑硬盘启动的。这样PSSD拿到其他电脑没启动。

我的解决方案是在安装电脑上进入Windows系统，然后使用DiskGenius工具导出电脑硬盘的EFI分区镜像，然后将镜像再导入移动硬盘的EFI分区。

然后移动硬盘拿到其他机器，通过移动硬盘启动就可以启动Ubuntu了。启动顺序进入每台机器的BIOS调整，将启动顺序1调整为移动硬盘，然后再是原机器的系统。

### 显卡驱动

一些机器的显卡可以即插即用，例如主芯片是AMD 3750H的笔记本。一些机器的显卡直接进入是VGA分辨率，很难看，需要安装显卡驱动。以我的一台电脑时候用的Nvidia 2080为例。

### 多显示器

## 基础软件

### 输入法

中文输入法是必需的软件，选择使用搜狗输入法

安装指南参见： https://pinyin.sogou.com/linux/help.php

注意记得要点那个应用到整个系统的按钮

另外搜狗输入法有些不稳定，这个要看运气，希望稳定的可以考虑使用谷歌拼音输入法

安装命令
``` bash
sudo apt-get install fcitx-googlepinyin
```

谷歌拼音输入法的设置流程也可以参考搜狗拼音输入法的帮助，键盘输入切换到fcitx。

### KeePass

KeePass是我一直使用的软件，提升密码安全性，每个网站/APP使用不同的密码，密码随机生成。这个软件有Linux版本。安装命令如下：

``` bash
sudo apt-add-repository ppa:jtaylor/keepass
sudo apt-get update
sudo apt-get install keepass2
```

### 词典

词典在所有平台我都使用欧路词典，跨平台同步数据，我也购买了收费版。比较好用。

官网地址： https://www.eudic.net/v4/en/app/eudic

直接下载deb用dpkg安装即可。

需要下载安装字典，建议建立一个专门存放字典的目录，然后下载的字典都放在这个目录下，然后用欧路字典添加这个目录即可。

## 开发环境

### 安装vscode
开发文档，C/C++开发等，我都使用vscode，比较好用。简单Java开发我也会用vscode

1. 安装依赖软件
``` bash
sudo apt update
sudo apt install software-properties-common apt-transport-https wget
```

2. 加入Microsoft GPG key
``` bash
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
```

3. 启用 Visual Studio Code 源仓库
``` bash
sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
```

4. 安装 Visual Studio Code 软件包
``` bash
sudo apt install code
```

后续可以通过apt更新

### Java开发环境

#### JDK安装

1. 下载JDK，官方下载：https://www.oracle.com/java/technologies/downloads/

2. 解压安装
例如解压到：$HOME/dev/jdk-17.0.1
为了切换jdk方便，建议创建一个链接
``` bash
ln -s $HOME/dev/jdk-17.0.1 $HOME/dev/jdk
```

3. 设置环境变量
在$HOME/.bashrc文件中添加如下环境变量
``` bash
export JAVA_HOME=/home/hjianhao/dev/jdk
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```

#### Maven安装

1. 下载Maven，官方下载：https://maven.apache.org/download.cgi

2. 解压安装
例如解压到：$HOME/dev/apache-maven-3.8.3
为了切换maven版本方便，建议创建一个链接
``` bash
ln -s $HOME/dev/apache-maven-3.8.3 $HOME/dev/jdk
```

3. 设置环境变量
``` bash
export M2_HOME=/home/hjianhao/dev/maven
export PATH=${PATH}:${M2_HOME}/bin
```

#### 安装vscode插件

1. 在扩展中搜索并安装“Extension Pack for Java”，包括了以下Java开发必要的插件
* Language Support for Java™ by Red Hat
* Debugger for Java
* Test Runner for Java
* Maven for Java
* Project Manager for Java
* Visual Studio IntelliCode

2. 设置JDK路径
在settings.json中设置(默认不设置就是\$JAVA_HOME指定的环境变量)
"java.home": "<path-to-jdk>"

3. 设置vscode的maven配置
在settings.json中设置
全局配置文件（默认不配置就是~/conf/settings.xml)
"java.configuration.maven.globalSettings": "<path-to-global-settings-file>"
用户配置文件(默认不配置就是 ~/.m2/settings.xml)
"java.configuration.maven.userSettings": "<path-to-user-settings-file>"
Maven可执行文件路径(指定Maven的版本，默认不配置就是PATH中可搜索到的执行文件)
""maven.executable.path": "<path-to-maven-executable>"

4. 设置本地仓库路径
默认不设置就是~/.m2/repository目录
如果需要就在用户配置文件中增加
``` bash
<settings>   
  <localRepository>本地仓库路径</localRepository>   
</settings> 
```

4.通过Ctrl+Shift+P，选择"Maven: Create Maven Project"创建Java工程
创建完工程就可以通过左边导航栏的的Maven视图执行Maven命令。或者通过Ctrl+Shift+P，选择"Maven: Execute Commands"执行命令。 如:clean install

vscode的Java开发简单介绍：https://code.visualstudio.com/docs/java/java-tutorial

#### 安装IDEA

直接下载安装IDEA即可，IDEA Java安装设置网上很多介绍

### C++开发环境

vscode的C/C++开发简单介绍：https://code.visualstudio.com/docs

### Markdown

Markdown我使用vscode + Markdown Preview Enhanced插件，自我感觉不错。可以根据自己的喜好使用其他不同插件.

## 音乐播放

### 流媒体

很多流媒体并没有提供Linux版本客户端，只提供了Windows和macOS客户端，只能使用Web客户端。

我常用的两个提供hires的流媒体服务Tidal和Qobuz都可以使用Web客户端，但是Tidal的Web客户端只能提供无损流媒体，并不能提供hires流媒体。

所以主要使用Qobuz的Web客户端，可以支持24/192的hires流媒体。但是目前只有FireFox浏览器默认不对hires音频进行转码。 Edge和Google浏览器会将音频重采样到16/44.1KHZ，所以不建议用这两个浏览器听。

FireFox设置是否转码是在about:config中设置，设置项是media.resampling.enabled。默认是false，如果是true就要修改为false。

Apple Music也支持Web客户端，但是Apple Music的Web客户端不支持无损和hires

QQ倒是有Linux客户端，但是基本也是Web客户端包装的Linux应用。

### 本地音乐

#### 挂接NAS
如果音乐存放在NAS上，则最好将NAS挂接到本地目录，例如将NAS上的my-music挂接到$HOME/Music/nas目录下。

1. 在NAS上使用SMB服务共享目录

2. 安装CIFS实用工具
``` bash
sudo apt install cifs-utils
```
3. 修改/etc/fstab
``` bash
sudo vi /etc/fstab
```
添加挂接规则
``` bash
# mynas
//<NAS-IP>/my-music /home/hjianhao/Music/nas cifs uid=<挂接目录的owner用户ID>,gid=<挂接目录的owner用户的组id>,dir_mode=0777,file_mode=0777,iocharset=utf8,vers=2.0,username=<NAS访问用户名>,password=<NAS访问密码> 0 0
```
需要注意的是：
* 挂接时最好指定目录为所有用户可见，以为后续运行mpd扫描时用的时mpd运行的用户
* 目录有中文要根据编码设置iocharset
* 使用id命令获取当前用户的uid和gid

4. 执行挂接
``` bash
sudo mount -a
```
因为是修改的是fstab文件，系统重启后依然生效

#### 本地播放器选择

我反复比较好，选择了mpd+gmpc。

Ubuntu自带的播放器Rhythmbox并不好用，也不太稳定。

试用了VLC，还行，但是功能不满意，尤其是音乐数据库管理。

我最终还是选择了mpd（服务端），客户端使用mpc（做命令行工具），gmpc（图形UI)，ncmpc（字符UI）。因为以前长时间使用volumio，他后端就使用mpd,同时mpd有插件支持DSD

##### 安装mpd
可以通过apt安装
``` bash
sudo apt install mpd
```
但是apt上mpd版本太旧了，所以我选择从源码编译安装，参见官方文档：https://mpd.readthedocs.io/en/stable/user.html#compiling-from-source

需要注意一点，因为mpd需要通过meson进行编译，然而apt安装的meson太低了，新的mpd不能使用，所以需要自己安装高版本的meson。参见官方文档：https://mesonbuild.com/Quick-guide.html

需要注意的是，如果不做任何修改，mpd会假设配置文件路径是：/usr/local/etc/mpd.conf，mpd的可执行文件是/usr/local/bin/mpd。然而在安装时，mpd的初始配置文件会拷贝到/etc/mpd.conf下，同时mpd的服务配置文件（/usr/lib/systemd/system/mpd.service）中可执行文件指向的是/usr/bin/mpd。这样会导致启动的时候找不到mpd命令或启动失败（找不到配置文件）

解决方案一：
在编译配置的时候执行：
```bash
meson configure output/release -Dsysconfdir='/etc' ; meson configure output/release
```

解决方案二：
如果你已经编译安装完了，可以将配置文件和可执行文件软连接到对应的位置
```bash
ln -s /usr/local/bin/mpd /usr/bin/mpd
ln -s /etc/mpd.conf /usr/local/etc/mpd.conf
``` 

##### 配置mpd
修改配置文件/etc/mpd.conf

1. 配置音乐文件所在目录
这个目录存放你的音乐，mpd扫描这个目录，并将音乐元数据加入数据库，这里我们将上面我们挂接的NAS目录配置上
music_directory         "/home/hjianhao/Music/nas"

2. 取消自动更新
auto_update    "no"
这个建议根据自己的使用习惯，我因为是将系统装到PSSD上，然后在不同地方使用，为了方便，就用VPN连接到家里的NAS，这样fstab就可以使用相同的内网IP，不用修改。因为VPN要手动连接，所以开机的时候并不能挂接上，此时音乐目录是空的，如果采用自动跟新，则会清空数据库里已经跟新的音乐数据，所以要将这里设置为取消

3. 添加音频设备
使用命令aplay -L列出你所有的音频设备，并添加到配置文件。寻找hw:打头的设备
例如我系统有如下设备：
```bash
hw:CARD=Device,DEV=0
    LuxuryPrecision UAC Device, USB Audio
    Direct hardware device without any conversions
```
每个设备需要添加一段audio_output配置
```bash
udio_output {
        type            "alsa"
        name            "LuxuryPrecision UAC Device"
        device          "hw:CARD=Device,DEV=0"
        auto_resample   "no"
        auto_channels   "no"
        auto_format     "no"
        dop             "yes"
}
```
name和device可以拷贝aplay输出的结果
dop让DSD音频通过DoP输出

4. 打开DSD插件
默认mpd的DSD支持是关闭的，通过以下配置打开
```bash
decoder {
        plugin                  "hybrid_dsd"
        enabled                 "yes"
}
```
5. 配置文件系统编码
如果的音乐文件使用非英文字符，就一定要配置正确的编码，要不然就容易出现乱码。
```bash
filesystem_charset              "UTF-8"
```

6. 配置播放列表文件的支持
```bash
playlist_plugin {
    name "m3u"
    enabled "true"
}
```

配置完成后重启mdp服务让其生效
```bash
sudo systemctl stop mpd
sudo systemctl start mpd
```

##### 使用mpd

客户端使用mpc（做命令行工具），gmpc（图形UI)，ncmpc（字符UI）

其中ncmpc我最常用，这个字符UI使用起来非常方便，各种快捷键操作
如果习惯图形UI就使用gmpc，支持专辑封面显示
mpc不是很常用，但是如果要配合脚本执行命令，就可以使用mpc命令行


## 社交
 人在社会就离不开社交，QQ和微信是必要的，但是微信没有Linux版本，QQ的Linux版本非常差。所以只能用wine模拟器运行Windows版本的微信和QQ。

### 微信与QQ

目前比较好的方案是使用deepin发布的微信和QQ。
用xDroid安装安卓版本的微信和QQ小问题太多。

``` bash
# 安装deep-in环境和添加源
wget -qO- https://deepin-wine.i-m.dev/setup.sh | sudo sh
# 安装微信
sudo apt-get install deepin.com.wechat 
# 安装QQ
sudo apt-get install deepin.com.qq.im    
# 安装TIM
sudo apt-get install deepin.com.qq.office
```

在微信输入中文的时候，可能会出现输入的文字的都是方框，我搜索了很多方法，以下方法我试用有效

1. 下载微软雅黑字体
下载的地方很多，下面地址可以下载
https://github.com/owent-utils/font/blob/master/%E5%BE%AE%E8%BD%AF%E9%9B%85%E9%BB%91/MSYH.TTC

2. 將字体复制到指定的位置
``` bash
cp /path/to/MSYH.TTC ~/.deepinwine/Deepin-WeChat/drive_c/windows/Fonts/msyh.ttc
```

3. 將字体注册到 Wine：
编辑~/.deepinwine/Deepin-WeChat/font.reg文件，输入以下内容
```bash
REGEDIT4
[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontSubstitutes]
"MS Shell Dlg"="msyh"
"MS Shell Dlg 2"="msyh"

[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontLink\SystemLink]
"Lucida Sans Unicode"="msyh.ttc"
"Microsoft Sans Serif"="msyh.ttc"
"MS Sans Serif"="msyh.ttc"
"Tahoma"="msyh.ttc"
"Tahoma Bold"="msyhbd.ttc"
"msyh"="msyh.ttc"
"Arial"="msyh.ttc"
"Arial Black"="msyh.ttc"
```

执行一下命令
```bash
~/.deepinwine/Deepin-WeChat/ deepin-wine regedit ~/.deepinwine/Deepin-WeChat/font.reg
```
备注：根据安装的版本不同deepin-wine可执行文件名字不一定相同，比如我安装的版本，上面的deepin-wine换为deepin-wine6-stable，具体可以到/usr/bin目录下搜索deepin看看。

4. 重启微信就可以正确输入中文了。 