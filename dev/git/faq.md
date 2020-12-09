
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [配置](#配置)
  - [git config作用范围](#git-config作用范围)
  - [配置用户和邮件](#配置用户和邮件)
  - [配置代理](#配置代理)
  - [不做证书认证](#不做证书认证)
  - [GitHub的Key配置](#github的key配置)
- [操作](#操作)
  - [将本地工程推送到git仓库](#将本地工程推送到git仓库)
  - [移动文件和目录](#移动文件和目录)
  - [删除文件夹](#删除文件夹)
  - [分支操作](#分支操作)
  - [添加文件](#添加文件)
    - [撤销添加](#撤销添加)
  - [如何找到所有未添加到库的文件](#如何找到所有未添加到库的文件)
- [遇到的问题](#遇到的问题)
  - [Ubuntu系统运行git与github服务器交互时出现 GnuTLS错误](#ubuntu系统运行git与github服务器交互时出现-gnutls错误)

<!-- /code_chunk_output -->

# 配置

## git config作用范围

* 默认作用范围: 当前使用仓库的 Git 目录中的 config 文件（即 .git/config）：针对该仓库。 你可以传递 --local 选项让 Git 强制读写此文件，虽然默认情况下用的就是它。。 （当然，你需要进入某个 Git 仓库中才能让该选项生效。）

* 操作系统用户作用范围(--global)：~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 你可以传递 --global 选项让 Git 读写此文件，这会对你系统上 所有 的仓库生效。

* 系统级范围(--system)：/etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 git config 时带上 --system 选项，那么它就会读写该文件中的配置变量。 （由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。）


## 配置用户和邮件

``` bash
git config --global user.name "hjianhao"
git config --global user.email "hjianhao@hotmail.com"

```

## 配置代理

设置代理
``` bash
git config --global https.proxy [http://127.0.0.1:1080](http://127.0.0.1:1080/)
git config --global https.proxy [https://127.0.0.1:1080](https://127.0.0.1:1080/)
```

取消代理
``` bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 不做证书认证

```
git config --global http.sslVerify "false"
```

## GitHub的Key配置

本机执行一下命令
```bash
ssh-keygen -t ed25519 -C "hjianhao@hotmial.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

然后将~/.ssh/id_ed25519.pub文件内容加入到github的SSH KEY中


# 操作

## 将本地工程推送到git仓库
``` bash
git init -- 新建一个本地仓库
git add README.md -- 将README.md文件加入到仓库中
git commit -m "first commit" -- 将文件commit到本地仓库
git remote add origin https://github.com/XuDaojie/Lee.git -- 添加远程仓库，origin只是一个远程仓库的别名，可以随意取
git push -u origin master -- 将本地仓库push远程仓库，并将origin设为默认远程仓库
```

## 移动文件和目录

将目录hello移动到electron目录下

``` bash
mkdir electron
mv hello electron
git add .
git commit -m "move hello dir to electron"
git push -u origin master
```

## 删除文件夹

``` bash
git rm <文件夹> -f -r
git commit -m "<注释>"
git push -u origin
```

## 分支操作

查看远程分支
``` bash
git branch -a
```

查看本地分支
``` bash
git branch
```

切换本地没有的分支
``` bash
git checkout -b <本地分支名称，如master> <远程分支名称，如origin/master>
```

切换本地已有的分支
``` bash
git checkout <本地分支名称>
```

## 添加文件

### 撤销添加

撤销所有添加的文件
``` bash
git reset HEAD
```

撤销某个错误添加的问题
``` bash
git reset HEAD <路径/文件名>
```

## 如何找到所有未添加到库的文件
```bash
git status --untracked-files=all
```
这里包括了所有子目录的未添加文件

# 遇到的问题

## Ubuntu系统运行git与github服务器交互时出现 GnuTLS错误

详细错误
error: RPC failed; curl 56 GnuTLS recv error (-110): The TLS connection was non-properly terminated.

原因：
Note: This solution is not just limited to codecommit but also for other Ubuntu gnults_handshake related issues.
If you have AWS cli installed in ubuntu 14.04 and working with AWS codecommit, you are likely to get “gnutls_handshake() failed” error when you try to clone a repository created in codecommit. Do not worry about it, we have a solution for it.

问题解决方案（亲自验证通过）
``` bash
# 安装必要的依赖及环境
sudo apt-get install build-essential fakeroot dpkg-dev
# 建个目录放 新编译的 git
mkdir ~/git-rectify
# 获取 git 源码
cd ~/git-rectify
sudo apt-get source git
# 安装 git 的依赖
sudo apt-get build-dep git
# 安装 libcurl
sudo apt-get install libcurl4-openssl-de
# 进入 git 目录，这里目录名视你安装的版本
cd git-2.7.4/
# 修改两个文件后重新编译 git
vim ./debian/control # 把libcurl4-gnutls-dev 修改为 libcurl4-openssl-dev
vim ./debian/rules # 把TEST=test整行删除
sudo dpkg-buildpackage -rfakeroot -b
# 回到上一级目录，安装编译好的安装包
cd .. # 也就是在 ~/git-rectify 目录下
sudo dpkg -i git_2.7.4-0ubuntu0.4_amd64.deb
```
