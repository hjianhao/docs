# Gradle

## 安装 

1. 下载Gradle离线安装zip包
2. 建立一个Gradle目录，将Gradlezip包拷贝到该目录下解压。
3. 在Gradle目录下创建一个repository目录用于保存下载的文件，目录结构如下：
```
D:\Dev\Gradle\gradle-6.7.1
D:\Dev\Gradle\repository
```
4. 设置环境变量GRADLE_PATH为D:\Dev\Gradle\gradle-6.7.1
5. 设置环境变量GRADLE_USER_HOME为D:\Dev\Gradle\repository
6. 将%GRADLE_PATH%\bin加入到PATH环境变量

## vscode插件安装

安装如下两个插件：
1. Gradle Language Support (Gradle文件语法高亮等）
2. Gradle Tasks (Gradle集成)

## vscode设置

为避免vscode的设置受环境变量影响，可以在settings.json中设置
```json
{
    "java.import.gradle.user.home": "D:\\Dev\\Gradle\\repository",
    "java.import.gradle.home": "D:\\Dev\\Gradle\\gradle-6.7.1",
}
```

## 使用Gradle Wrapper时加速下载Gradle


在使用Gradle Wrapper固定Gradle版本时，初始配置会去下载Gradle的完整包，速度比较慢。
可以停止下载，然后再
在%GRADLE_USER_HOME%\wrapper\dists\gradle-6.7.1-all目录下会发现一个随机目录，类似：2moa8rlfac5eqlcfgk98k0deb
进入这个目录将下载的临时文件删除。
然后手工下载gradle-x.y.z-all.zip这个包（可以用工具加速），然后放到这个目录下。
然后再进行配置就可以直接使用这个下载好的包完成后续的配置。


## Debug Gradle build生成的代码

添加Java Debug的配置后，调试时默认会从project/bin目录下取找class文件，如果是由gradle build生成的话，class文件是在build/classes目录下
需要修改launch.json文件，设置class文件目录(classPaths)
```json
        {
            "type": "java",
            "name": "Debug (Launch)-App<app>",
            "request": "launch",
            "mainClass": "net.hjianhao.hello.App",
            "projectName": "app",
            "classPaths": ["app/build/classes/java/main"]
        }
```

# Java

## 插件安装

1. Debugger for Java (调试）
2. Java Test Runner (测试用例运行）
3. Language support for Java ™ for Visual Studio Code （Java特性支持）
4. Java Extension Pack （包括Java开发需要的插件）
5. Maven for Javag （Maven支持）


# 参考材料

Debugger for Java项目：https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug
Gradle Tasks项目： https://github.com/badsyntax/vscode-gradle
