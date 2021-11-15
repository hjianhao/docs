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

1. Core : 系统的大脑。它管理控制和输出组件，并跟踪音乐库中的内容。
2. Control : 用户浏览/发现音乐，并控制核心组件将音乐输出到输出组件
3. Ouput : 音乐发声组件，将核心输出信号变成声音播出


## Roon系统上下文

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

core <-- tidal : 读取用户数据\n获取流媒体音乐
core <-- qobuz : 读取用户数据\n获取流媒体音乐

package "Net Disk" {
    cloud "Dropbox" as dropbox
}

core <-- dropbox : 获取流媒体音乐

node "Roon APP\n(PC, Tablets, Phone)" as app

app --> core : 浏览/发现音乐\n播放音乐

package "Audio Devices（音响厂商）" {
    node "Roon Tested Devices" as test
    node "Other Connected Devices\n(USB DAC,\n Sound Card,\n)" as connect
    node "Roon Ready \nNetworked devices" as ready
    node "Other Networked Devices\n(AirPlay devices,\nHQ Player,\nMeridian Audio,\nLinn,\nSqueezBox,\nChromecast)" as other
    test -[hidden]=connect
    connect -[hidden]=ready
    ready -[hidden]-other
}

core -l-> test : 播放流媒体音乐\n(USB/HDMI)
core -l-> connect : 播放流媒体音乐(USB/PCIE etc.)
core -l-> ready : 播放流媒体音乐\n(RAAT协议， LAN/WiFi)
core -l-> other : 播放流媒体音乐\n(LAN/WiFi)

package "Metadata services" {
    cloud "TiVo" as tivo
}

package "Display Devices" {
    node "Chromecast/\nWeb Browser" as display
}

core -u-> display : 显示当前播放的歌曲封面\n，艺人照片，歌词
roon <-u- tivo : 获取元数据

actor ”用户" as user 

user --> app : 浏览/发现音乐\n播放音乐

```

## Roon 核心

核心就是系统的大脑。它管理控制和输出组件，并跟踪音乐库中的内容。

核心负责：

1. 管理您的数字音乐库

    * 发现您的音乐文件

    * 观测实时添加，删除或修改音乐文件的时间

    * 从音乐文件中提取元数据标签

    * 音频内容的背景分析

    * 识别您的音乐文件，以便我们可以通过插图，字幕，评论和其他增强的元数据来改善您的音乐库。 

    * 随着新信息的可用和功能的添加，使元数据保持最新。

2. 管理音频输出的回放  

    * 管理每个区域的播放队列和一组控件 。 

    * 管理输出到多个 区域，包括同步播放 

    * 从文件或Internet服务中检索音频并将其解码为PCM或DSD

    * 将PCM或DSD音频流传输到输出

3. 驱动一个或多个控件

    * 向控件提供数据，无论控件是本地控件还是网络控件

    * 响应控件的请求

    * 提供跨多个控件的同步体验

4. 其他的东西 

    * 处理软件更新

## RAAT协议

什么是RAAT？
RAAT（Roon高级音频传输）是Roon音频分发技术的基础。它旨在支持各种软件和硬件应用程序，而没有格式支持限制或质量妥协。通过与显示器，按钮，音量控制和信号源切换功能相集成，它提供了丰富的用户体验。它使您可以播放Roon音乐：

1. 支持Roon的 硬件设备

2. 音频输出，声卡和连接到Core的USB DAC 。

3. 连接到任何运行Roon Bridge的计算机或设备的音频输出，声卡和USB DAC

4. 音频输出和USB DAC连接到运行Roon Remote的Android设备。

ROON将RAAT视为发烧友的AirPlay。2015年11月，ROON向硬件制造商发布了Roon Ready SDK，使他们能够创建使用RAAT的硬件产品。从那时起，已经推出了100多种Roon Ready产品，并且正在不断开发更多产品。

RAAT设计目标

1. 在今天和可预见的将来支持所有相关的音频格式。ROON不会发布RAAT支持的格式列表，因为它不是限制因素。

2. 以太网和WiFi网络上的稳定流。ROON现在认为这是理所当然的，但是说起来容易做起来难，而且这项要求驱动了很多实现选择。

3. 端点硬件要求适中。这意味着端点不必处理昂贵的DSP或内容解码-这将在服务器上发生。这意味着许多现有设备都可以添加对RoonReady的支持，而无需更改硬件。

4. 音频设备必须拥有音频时钟。许多其他协议都会弄错这一点，包括AirPlay。两个时钟不可能完全一致。让DAC控制流的速度可以消除对时钟漂移补偿机制的需求，该机制势必会增加成本，降低音质或两者。

5. 严格的播放同步，适合多房间聆听。这里有一条小心的线。如果ROON需要超紧密（1-10us）同步，则不可能在现有/非专业/异构硬件平台上实施该系统。我们的拍摄时间应在1毫秒之内（在理想情况下通常要好得多），这足以满足多房间聆听的需要。

6. 输出端无需固件升级即可支持全新流服务，文件格式，DRM方案等的支持。实际上，唯一需要升级的原因是修复低级错误或访问更多硬件功能。这非常重要。并非所有合作伙伴/硬件都具有可以在家完成的简便固件更新。ROON对这一现实的接受深刻影响了RAAT的设计。与Google的Cast设备一样，大多数业务逻辑都是在运行时作为脚本传递到设备的。这意味着ROON能够完全重新设计音频流和缓冲逻辑，而无需更新设备固件。这是绝对关键的，因为在这样的系统中，大多数错误+演变都与网络有关，而不是音频。除了Cast之外，ROON没有发现还有其他具有这种灵活性的系统。 

7. 实施便宜，易于分发。不涉及专利技术。无需制造商使用受出口限制的技术。Roon提供高质量，可移植的参考实现作为自定义的基础，而不是一堆描述网络协议的文档。

8. 提供出色的用户体验。这意味着触摸传输控件时不会出现愚蠢的2秒延迟（看着您，AirPlay）。这意味着没有太简单的区域同步方法（看着你，squeezebox）。这意味着没有人为的流格式限制。这意味着系统足够灵活，可以在服务器或端点中进行处理。这意味着音量控制和音源选择在任何可能的时候都可以正常工作。 

9. 提倡关于音频播放方式真实呈现。RAAT与Roon的信号路径功能相关。ROON与制造商合作，以确保潜在的破坏性处理阶段（例如软件音量控制）展示给感兴趣的用户，并且不会隐藏或隐藏处理。

10. 通过认证计划增强高质量的用户体验。用户体验是ROON的另一项核心能力。ROON正在积极推动硬件公司通过在允许发布之前在产品上进行迭代来改善用户体验。ROON要求RoonReady集成与设备提供的其他音频协议之间具有均等性，以确保Roon支持不会成为二等公民。认证计划的另一个要求是，硬件制造商应长期为Roon提供设备支持和质量保证。

11. 双向控制集成。艺术品和正在播放的信息可以显示在硬件设备上。前面板控件和红外遥控器可以通过设备控制Roon。设备前面板上的音量控制可以与Roon保持同步。如果您正在与具有多个输入的设备通话，并在Roon中启动音乐，则输入会自动切换为Roon的输入。

12. 深度可扩展的协议。ROON在硬件协议以及RAAT实现和特定于硬件的代码之间的接口中放置了许多扩展点。这使ROON将来可以轻松支持更多功能。ROON完全期望随着ROON支持的硬件的广度增加而了解更多用例，并且协议旨在避开障碍并优雅地扩展。 

13. 不支持规格不足的平台或未经验证的网络堆栈。RAAT旨在随着时间的推移而发展。ROON将继续改进网络协议。ROON可能决定更改设备上的缓冲区大小要求以提高稳定性。ROON可能决定建立第二个网络协议，该协议针对通过WAN或其他类似方式进行流传输进行了优化。ROON向用户提供的建议与向制造基于RAAT的产品的制造商提供的建议相同。硬件比以往任何时候都便宜，而且一直在便宜。如果您想要最好的结果，请不要过度节约。