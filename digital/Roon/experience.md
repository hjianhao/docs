# 我之前听音系统的网络拓扑
``` plantuml

node "PS Audio PWD\n(解码器\n24/192)" as dac {
    component "Network Bridge\n(Media Render)" as bridge
    component "解码板" as bdac
    bridge --> bdac : I2S
}

node "Krell S550i\n(功放)" as amp
bdac --> amp : XLR(平衡输出)

node "ATC SCM 19\n(音箱）" as speaker
amp --> speaker

node "群晖NAS\n(J1900四核/8G内存,\n4*4T Raid5阵列)" as nas {
    component "Audio Station\n(Media Server)" as mserver
    mserver -> bridge : DLNA (24/192)\n有线
    component "WebDav Server" as wserver
    wserver -[hidden]r-> mserver
    database "Storage" as storage
    wserver <--> storage : 读写数据
    mserver --> storage : 读写数据
}

node "客厅PC" as pc {
    component "Foobar 2000\n(Brower and Controller)" as f2_1
    f2_1 <--> mserver : DLNA（有线)
}

node "书房MAC" as mac {
    component "Kodi\n(Media Render, \nBrowser and Controller)" as kodi
    kodi <--> mserver : 控制, 获取音频（无线)\nDLNA (32/384, DSD256)
}

node "NuPrime id-8" as dac1
kodi -u=> dac1 : USB <-> DAC
node "丹拿52SE" as speaker1
dac1 -> speaker1 

node "家庭局域网手机/Pad" as phone {
    component "DLNA客户端\n(Brower and Controller)" as dc_1
    dc_1 <--> mserver : 控制(无线)
}

node "小朋友房间\n Huawei Sound X" as soundx
mserver <-u-> soundx : DLNA\n(24/96)\n (无线)

node "卧室一对\n Huawei Sound X" as soundx1
mserver <--> soundx1 : DLNA\n(24/96)\n (无线)

cloud "广域网" as wan
wserver <-l-> wan

node "远程PC" as rpc {
    component "RaiDrive\n (WebDav Client)" as rai
    rai <-u-> wan
    component "Foobar 2000\n(Brower and Controller)" as f2_2
    f2_2 -u-> rai : 读取音频数据
}

node "海贝R6Pro\n(DSD256,\n 32/384,\n MQA)" as hiby
f2_2 <--> hiby : USB <-> DAC

node "远程手机" as rphone {
    component "海贝音乐\n（自带WebDav Client)" as mhiby
    mhiby <-u-> wan : 转发
}

```


# 我的Roon听音系统网络拓扑

``` plantuml

node "PS Audio PWD\n(解码器\n24/192)" as dac {
    component "Network Bridge" as bridge
    component "解码板" as bdac
    bridge --> bdac : I2S
}

node "Krell S550i\n(功放)" as amp
bdac --> amp : XLR(平衡输出)

node "ATC SCM 19\n(音箱）" as speaker
amp --> speaker

node "群晖NAS\n(J1900四核/8G内存,\n4*4T Raid5阵列)" as nas {
    component "Roon Server" as roon
    component "squeeze2upnp" as s2u
    roon --> s2u : squeezebox协议
    s2u --> bridge : DLNA (24/192)\n有线
    component "L2TP/IPSec Server" as svpn
    roon <--> svpn : 转发
}

node "客厅PC" as pc {
    component "Roon APP" as roon3
    roon3 <--> roon : 控制（有线)
}

node "书房MAC" as mac {
    component "Roon APP" as roon5
    roon5 <--> roon : 控制（无线)
}

node "NuPrime id-8" as dac1
roon5 -u=> dac1 : USB <-> DAC
node "丹拿52SE" as speaker1
dac1 -> speaker1 

node "家庭局域网手机/Pad" as phone {
    component "Roon APP" as roon4
    roon4 <--> roon : 控制(无线)
}

node "小朋友房间\n Huawei Sound X" as soundx
s2u <--> soundx : DLNA\n(24/96)\n (无线)

node "卧室一对\n Huawei Sound X" as soundx1
s2u <--> soundx1 : DLNA\n(24/96)\n (无线)

cloud "广域网" as wan
svpn <--> wan

node "远程PC" as rpc {
    component "VPN Client" as cvpn1
    cvpn1 <-u-> wan
    component "Roon" as roon1
    cvpn1 <--> roon1 : 转发
}

node "海贝R6Pro\n(DSD256,\n 32/384,\n MQA)" as hiby
roon1 <--> hiby : USB <-> DAC

node "远程手机" as rphone {
    component "VPN Client" as cvpn2
    cvpn2 <-u-> wan
    component "Roon Remote" as roon2
    cvpn2 <--> roon2 : 转发
}

```



# 如何为Roon增加DLNA输出

Roon本身不支持输出，但是支持Squeezebox设备（就是LMS（Logitech Media Server）体系）

思路就是做一个桥，将Roon使用Squeezebox协议输出的音频转成DLNA的协议

使用这个软件实现：Squeeze2upnp，他的目的就是将DLNA设备变成SqueezeBox设备

``` plantuml
node "Roon Remote" as remote
node "Roon Core" as core
node "Squeeze2upnp\n(pass through or resample)" as s2u
node "DLNA render\n(eg. Huawei Sound X) " as render

remote --> core : play control
core -> s2u
s2u -> render : DLNA protocol
```

软件github地址：https://github.com/philippe44/LMS-to-uPnP
里面有下载地址和提供支持的论坛地址



## 安装

安装非常简单，从github网址给出的下载网站下载最新的安装包即可。

安装包解压后有一个Bin目录，所有平台的可执行文件都在里面。
尽管Squeeze2upnp是LMS的一个插件，但是他并不依赖LMS，可以独立运行。解压后在Bin目录下后缀为_static的可执行程序都是独立执行程序

Squeeze2upnp支持Windows, OSX, or Linux x86/64, ARM and OSX

可以从可执行的程序名中看出哪个可执行程序对应什么平台。

例如树莓派，你可以选择 ： squeeze2upnp-armv6hf-static
例如群晖X86 NAS， 你可以选择 ： squeeze2upnp-x86-64-static

对于Linux，对应你平台的执行文件需要增加可执行属性，例如
``` bash
chomd a+x squeeze2upnp-x86-64-static
```

以下描述以都以群晖NAS为例，其他的平台将命令中的可执行程序替换为对应平台的执行程序即可。

## DLNA设备发现

对于第一次使用，或者希望重新创建一个配置文件，需要执行以下命令:
``` bash
squeeze2upnp-x86-64-static -i config.xml
```

等程序执行完，就会生成一个基础配置文件（config.xml，这个名字可以按照你的指定更换），里面包括默认配置和发现的你的局域网的DLNA设备。

其中<common>...</common>段是针对你所有设备的通用配置

每个发现的设备都有一个<device>...</device>段，你可以在<device>段中设置和common中相同的参数，这个参数会针对这个设备覆盖common参数。
例如, 在<common>段设置了设备最大支持48Khz采样率， 但是如果你的设备的最大采样率是192Khz，你可以在<device>段进行覆盖:
``` xml
<common>
  ...
  <sample_rate>48000</sample_rate>
  ...
</common>
<device>
  ...
  <sample_rate>192000</sample_rate>
  ...
</device>
```

每个被发现的设备的<enable>属性都是1，表示会为这个设备生成一个桥设备，在Roon设置里面的音频设备列表展现， 如果你不想在Roon中使用这个设备，就将enable设置为0.

<device>段里面有一个有个属性是DLNA Render的标识，类似:
```xml
<mac>bb:bb:e6:4a:a6:13</mac>
```
这是这个设备的标识，后续发现设备时，设备mac地址和这个段相同，就不会添加新的设备，并使用这个<device>设置的属性进行处理。

！！！！！！**最重要的一点: 将roon_mode属性设置为1，否则Roon下可能不工作**
```xml
<roon_mode>1</roon_mode>
```

后续有新设备发现，squeeze2upnp会为新设备增加一个device段到配置文件。如果你不想自动增加新设备（可能会造成问题），可以将<common>段的<enabled>属性设置为0

## 主要的配置


## 运行

测试运行：
```bash
squeeze2upnp-x86-64-static -x config.xml
```
调试信息会输出在终端

正式运行
```bash
squeeze2upnp-x86-64-static -z -x config.xml
```
**要用-z参数在后台运行，否则即使使用Linux在后台运行，会占用一个CPU核的全部资源**

然后需要开机启动就将上诉命令加入到/etc/rc.local 中即可



