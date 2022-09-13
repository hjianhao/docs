```plantuml

rectangle "客厅" {
    rectangle "左音箱\nAudio Vector\nR3签名版" as lspk1
    rectangle "右音箱\nAudio Vector\nR3签名版" as rspk1
    rectangle "功放\n关氏 MA1金版" as MA1
    lspk1 - MA1 : 喇叭线
    MA1 - rspk1 : 喇叭线
    rectangle "解码器\nBurson Conductor Virtuoso" as dac1
    MA1 -- dac1 : RCA
    rectangle "黑胶唱机\SONY PS-HX500" as table
    MA1 -- table : RCA
    rectangle "CD机\Revox B226-S" as cd1
    MA1 -- cd1 : RCA
    rectangle "界面\nDenafrips GAIA" as gaia
    dac1 -- gaia : 同轴
    rectangle "小主机\ndaphile系统" as daphile
    gaia -- daphile : USB
    rectangle "电视" as tv
    dac1 -- tv : 光纤
    rectangle "NAS(黑群晖)\nDLNA服务器\nRoon Core" as nas
    daphile -- nas : SMB/网线/交换机
    rectangle "Roon Bridge\n树莓派4b" as bridge1
    gaia -- bridge1 : AES
    bridge1 -- nas : RAAT/网线/交换机

    rectangle "手机/Pad/PC" as ctr1 {
        rectangle "浏览器" as browser
        browser -- daphile : 浏览器控制\nWIFI/网线
        rectangle "Roon APP" as app
        app -- nas : APP控制\nWIFI/网线
    }    
}

rectangle "书房" {
    rectangle "左音箱\nAudio Vector\nR3签名版" as lspk2
    rectangle "右音箱\nAudio Vector\nR3签名版" as rspk2
    rectangle "解码功放一体机\n新派 ida-8" as ida8
    lspk2 - ida8 : 喇叭线
    ida8 - rspk2 : 喇叭线
    rectangle "PC\nfoobar2000" as pc
    ida8 -- pc : USB
    pc -- nas : WIFI/SMB
}

rectangle "卧室" {
    rectangle "左音箱\nAudio Vector\nR3签名版" as lspk3
    rectangle "右音箱\nAudio Vector\nR3签名版" as rspk3
    rectangle "后级\n希腊神曲 Artemis" as artemis
    lspk3 - artemis : 喇叭线
    artemis - rspk3 : 喇叭线
    rectangle "解码\nPS Audio PerfectWave DAC(PWD)\nBridge卡" as dac2
    artemis -- dac2 : RCA
    rectangle "CD机\nPS Audio PerfectWave Table(PWT)" as cd2
    dac2 -- cd2 : I2S
    dac2 - nas : DLNA

    rectangle "手机/Pad/PC" as ctr2 {
        rectangle "bubble upnp APP" as upnp
        upnp -u- dac2 : WIFI/DLNA/控制播放
    }        
}

cloud "Qobuz"  as qobuz

nas -- qobuz : 互联网/Roon

upnp -- qobuz : 互联网/Qobuz协议\n读取到Qobuz音乐转换DLNA推送

```