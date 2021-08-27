# Roon是什么

Roon是一个音乐平台，他包含了
1. 音乐数据库：让使用者能了解所听的音乐，以及音乐背后的歌手，乐队，创作者等各种信息
2. 音乐组织功能：自动导入音乐;非常方便的过滤、排序、搜索音乐;自动监控你的音乐目录文件内容变化并刷新数据库;关联专辑的多个版本;提供播放列表，标签等手段组织音乐等。
3. 音乐流媒体: 可以将本地音乐（U盘，硬盘），局域网音乐（NAS，其他设备文件共享），网络流媒体（TIDAL, Qobuz），网盘（Dropbox）的音乐集中管理并流到音响设备。
4. 丰富的音响设备生态： 可以将音乐通过网络流向Roon Ready设备，通过USB流向Roon Tested设备。使用Roon Ready和Roon Tested设备，可以查看到音乐在整个stream路径中的情况（哪里损失了音质，哪里增强了音质）。 目前大量的主流音响厂商都通过Roon Ready和Roon Tested测试，还有大量设备在通过。
5. 多房间音乐播放：通过一个核心，多个桥（每个房间一个，Roon Ready音响设备自带桥）的方式，支持多歌房间播放音乐。多歌房间可以播放相同的音乐，也可以播放不同的音乐。 通过VPN，可以在异地的房间播放音乐。
6. 个性化管理和推荐： 支持按照profile给家庭多个用户进行个性化定制。

# Roon的核心概念

Roon的构成有主要有

1. Roon Core : 


``` plantuml

title "Roon 系统上下文"

cloud Roon as roon

package "Music Streaming Services" {
cloud TIDAL as tidal
cloud Qobuz as qobuz
}

node "Roon Core" as core

core <-u-> roon : 1. 获取元数据\n2.获取用户数据


package "Local Music storage" {
    node "U盘" as usb
    node "NAS" as nas
}

core <-- nas : 读取音乐文件
core <-- usb : 读取音乐文件

core --> tidal : 读取用户数据\n获取流媒体音乐
core --> qobuz : 读取用户数据\n获取流媒体音乐

package "Net Disk" {
    cloud "Dropbox" as dropbox
}

core --> dropbox : 获取流媒体音乐

node "Roon APP\n(PC, Tablets, Phone)" as app

app --> core : 浏览/发现音乐\n播放音乐

package "Audio Devices" {
    node "Roon Tested Devices" as test
    node "Roon Ready Devices" as ready
    node "Other Devices" as other
    test -[hidden]=ready
    ready -[hidden]-other
}

core -l-> test : 播放流媒体音乐\n(USB/HDMI)
core -l-> ready : 播放流媒体音乐\n(RAAT协议， LAN/WiFi)
core -l-> other : 播放流媒体音乐\n(Airplay,Linn,SqueezeBox协议,\n LAN/WiFi)

package "Metadata services" {
    cloud "TiVo" as tivo
}

package "Display Devices" {
    node "Chromecast" as chromecast
}

core -u-> chromecast : 显示当前播放的歌曲封面，艺人照片，歌词
roon -u-> tivo : 获取元数据

actor ”用户" as user 

user --> app 
```
