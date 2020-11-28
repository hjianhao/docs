# 软件管理

## F-Droid

清华源 : https://mirrors4.tuna.tsinghua.edu.cn/help/fdroid/

# 终端

## Termux
这个软件从F-Droid下载可以安装

### 切换国内源

清华源 : https://mirror.tuna.tsinghua.edu.cn/help/termux/

### 访问存储卡
执行如下命令:
```bash
termux-setup-storage
```
然后会提示授予访问存储卡的权限，同时生成storage目录。其中storage/shared目录下就是存储卡的目录了。

### 访问github

使用Termux可以通过apt安装openssh, git，然后通过openssh命令生产SSH-KEY，然后添加到github，接下来就可以通过git命令对github的仓库进行操作了。

# 文档编辑

## Markdown

当前选择了Markor配合Termux对github上的markdown文档进行编辑，从F-Droid安装。 
