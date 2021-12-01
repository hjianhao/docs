


<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [概述](#概述)
- [折腾过程](#折腾过程)
  - [操作系统](#操作系统)
    - [分区与引导](#分区与引导)
    - [显卡驱动](#显卡驱动)
    - [多显示器](#多显示器)
  - [基础软件](#基础软件)
    - [输入法](#输入法)
    - [KeePass](#keepass)
    - [词典](#词典)
  - [开发环境](#开发环境)
    - [Java开发环境](#java开发环境)
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
    - [xDroid](#xdroid)
    - [微信与QQ](#微信与qq)

<!-- /code_chunk_output -->

# 概述

作为一个长期的Windows和macOS桌面用户，我基本没有接触过Linux的桌面环境，但是工作和学习中长期使用Linux作为服务器操作系统部署生产和测试环境。突发奇想，想看看现在的Linux的桌面环境是否可以作为工作和学习环境，于是我就试着搭建了一下。

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

操作系统选择的是Ubuntu，应该区别不大，也算是最流行的Desktop发行版之一。



### 分区与引导

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


### 显卡驱动

### 多显示器

## 基础软件

### 输入法

### KeePass

### 词典

## 开发环境

### Java开发环境

### C++开发环境

### Markdown

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

### xDroid

### 微信与QQ
