# 我的Roon网络拓扑

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

node "黑群晖NAS" as nas {
    component "Roon Server" as roon
    component "squeeze2upnp" as s2u
    roon -r-> s2u : squeezebox协议
    s2u -> bridge : DLNA (24/192)\n有线
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
roon <-u-> soundx : DLNA\n(24/96)\n (无线)

node "卧室一对\n Huawei Sound X" as soundx1
roon <--> soundx1 : DLNA\n(24/96)\n (无线)

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

