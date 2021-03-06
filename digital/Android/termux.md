
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Termux](#termux)
  - [Termux](#termux-1)
  - [切换国内源](#切换国内源)
  - [UI](#ui)
    - [定制终端颜色和字体](#定制终端颜色和字体)
  - [访问存储卡](#访问存储卡)
  - [访问github](#访问github)
  - [安装linux](#安装linux)
- [Ubuntu](#ubuntu)
  - [换源](#换源)
  - [存储卡限制](#存储卡限制)

<!-- /code_chunk_output -->


# Termux

## Termux
这个软件从F-Droid和Google商店下载可以安装
F-Droid插件下载免费

## 切换国内源

清华源 : https://mirror.tuna.tsinghua.edu.cn/help/termux/

## UI

### 定制终端颜色和字体
长按termux窗口，选择“More”，然后选择sytle，然后就可以选择主题和字体。有e-ink模式。
这个要安装插件，从Google商店下载要18港币。

## 访问存储卡
执行如下命令:
```bash
termux-setup-storage
```
然后会提示授予访问存储卡的权限，同时生成storage目录。其中storage/shared目录下就是存储卡的目录了。

## 访问github

使用Termux可以通过apt安装openssh, git，然后通过openssh命令生产SSH-KEY，然后添加到github，接下来就可以通过git命令对github的仓库进行操作了。

## 安装linux

``` bash
pkg install proot
pkg install proot-distro
proot-distro install <alias>
```
其中发行版别名如下
proot-distro list - show the supported distributions and their status.
proot-distro install - install a distribution.
proot-distro login - start a root shell for the distribution.
proot-distro remove - uninstall the distribution.
proot-distro reset - reinstall the distribution.

# Ubuntu

## 换源

清华源 : https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu-ports/
因为默认安装没有安装证书，在换源前要安装证书，否则不能识别清华源的证书
```bash
apt install --reinstall ca-certificates
```

## 存储卡限制

如果手机或者平板的存储卡是fat或者ntfs格式的，则编译生成的可执行文件没有可执行属性，也不能通过chmod增加可执行属性。
所以如果需要设置文件属性的，需要将文件放在ubuntu系统中，例如放在用户目录下。

