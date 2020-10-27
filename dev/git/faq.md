# 配置

## git config作用范围

* 默认作用范围: 当前使用仓库的 Git 目录中的 config 文件（即 .git/config）：针对该仓库。 你可以传递 --local 选项让 Git 强制读写此文件，虽然默认情况下用的就是它。。 （当然，你需要进入某个 Git 仓库中才能让该选项生效。）

* 操作系统用户作用范围(--global)：~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 你可以传递 --global 选项让 Git 读写此文件，这会对你系统上 所有 的仓库生效。

* 系统级范围(--system)：/etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 git config 时带上 --system 选项，那么它就会读写该文件中的配置变量。 （由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。）


## 配置用户和邮件

```
git config --global user.name "hjianhao"
git config --global user.email "hjianhao@hotmail.com"

```

## 不做证书认证

```
git config --global http.sslVerify "false"
```
