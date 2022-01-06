# 安装

## 安装JDK

官方Java SE下载网址 : https://www.oracle.com/java/technologies/javase-downloads.html

解压:
``` bash
tar xvfz <下载的jdk文件 xxx.tar.gz>
```

设置JDK环境变量

修改用户的bash配置文件。 $HOME/.bashrc，添加：
``` bash
export JAVA_HOME=$HOME/Dev/JDK15
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

# 编译OpenJDK 

以下以在Windows下的WSL的Ubuntu系统为例:

## 获取源码

从Github获取：
```bash
git clone https://git.openjdk.java.net/jdk/
```

目前Github访问并不顺畅，而且速度也比较慢，国内建议从Gitee获取
```bash
git clone https://gitee.com/mirrors/openjdk.git
```

然后用git命令切换到你要编译的OpenJDK版本，例如切换到OpenJDK 17版本并创建分支
```bash
git checkout -b jdk17 jdk-17-ga
```

后续编译可以参考下载源码的doc/building.html

## 准备编译所需的软件

除了building.html中提到的软件外，还有一些软件需要安装，因为不同Linux发行版本预装的软件不一样，以我的Ubuntu 20.04为例
``` bash
sudo apt install zip unzip
sudo apt-get install libfontconfig1-dev
sudo apt install systemtap-sdt-dev # 使用dtrace选项需要
```

## 配置已经编译

1. 使用在源代码目录下使用configure进行配置
```bash
bash configure --enable-debug --with-jvm-variants=server --enable-dtrace --build=x86_64-unknown-linux-gnu --host=x86_64-unknown-linux-gnu
```

在WSL下编译，需要在configure时增加参数--build=x86_64-unknown-linux-gnu --host=x86_64-unknown-linux-gnu

2. 配置成功后使用make images进行编译

## 检查编译结果
执行Java命令
``` bash
./build/*/images/jdk/bin/java -version
```

执行测试
make run-test-tier1



