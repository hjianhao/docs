# 如何修改Kindle的字体

1. 支持OTF和TTF
2. 字体放在fonts目录下

参考：https://bookfere.com/post/655.html

# 使用自定义字典

1. 在下面参考网址下载字典
2. 字典放在"documents\dictionaries\"目录下

参考：https://bookfere.com/dict

# 网站

书伴： https://bookfere.com/

# Kindle书源构成

``` plantuml

node Kindle as kindle {
    component 浏览器 as kbrow
    component 商店 as kshop
    database 本地书库 as kllib
    kshop --> kllib : 购买后下载
    kbrow --> kllib : 下载后保存
}

node 手机 as Phone {
    component 局域网精灵 as lgenius
    component 百度网盘APP as baiduclient
    component ES文件浏览器 as esfb
    component "阅读APP\n(追网文)" as openreader
    component KAF as kaf
    component 邮件客户端 as mailclient
    component "浏览器" as pbrow
    component "微信Send to Kindle服务" as s2k
    database "本地书库\n(txt,mobi,azw3,pdf...)" as pllib
    
    kbrow -> lgenius : 下载书籍\n(mobi, azw3...)
    lgenius --> pllib : 读取本地书库
    esfb --> pllib
    baiduclient --> pllib
    openreader --> pllib : 将缓存导出TXT
    kaf -> pllib : 读取TXT转换为mobi
    mailclient --> pllib
    lgenius -[hidden]> mailclient
    mailclient -[hidden]> esfb
    esfb -[hidden]> baiduclient
    lgenius -[hidden]-> kaf
    baiduclient -[hidden]r-> openreader
    pbrow -[hidden]-> s2k
    kbrow -> kaf : 下载书籍\n(mobi)
}

cloud 百度网盘 as baiducloud
baiduclient -u-> baiducloud : 搜到、上传和\n下载电子书
esfb -u-> baiducloud : 搜到、上传和\n下载电子书

cloud "支持WebDav的网盘\n(如坚果云)" as webdavcloud
esfb -u-> webdavcloud : 上传和\n下载电子书

cloud 微信读书云 as weread
kbrow -u-> weread : 微信读书kindle版网页

cloud "KAF服务器\n(可自己搭建)" as kafcloud
kaf --> kafcloud : 上传txt转换mobi

cloud "亚马逊文档服务" as adcloud
mailclient -u-> adcloud : 发送电子书
adcloud --> kllib : （转换）发送到kindle

cloud "Kindle书店" as kshopcloud
kshop -u-> kshopcloud : 购买/租借图书

cloud "支持推送的网站\n如volmoe(漫画)、RSS等" as pushcloud
pbrow -u-> pushcloud : 点击推送
pushcloud -> adcloud : 推送文档

cloud "Send to Kindle服务" as s2kcloud
s2k -u-> s2kcloud : 发送网址

cloud "网页" as webpage
s2kcloud -u-> webpage : 获取网页并转换为kindle文档
s2kcloud -> adcloud : 发送转换后的网页

cloud "Google GAE" as gae {
    component KindleEar as kear
    kear -> adcloud : 定时推送RSS
    pbrow -u-> kear : 订阅RSS
}

```