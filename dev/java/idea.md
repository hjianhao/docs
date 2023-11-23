# 工程设置

打开工程设置 File -> Project Structure...

## 设置Java版本兼容性

Project Settings -> Project -> Project language level

例如你使用JDK16，可以设置你的项目使用的特性兼容JDK11,JDK11以上的语言特性不能使用


# Java设置

## 自动导入

1. 打开Settings
2. 点击Editor->General->Auto import
3. 勾选以下两项
    * Add unambiguous imports on the fly
    * Optimize imports on the fly

# 操作快捷键

## 搜索

搜索 : Ctrl+ Shift + N, 然后选择要搜索的内容，如类

搜索类： Ctrl + N

# WSL

官方WSL说明：
https://www.jetbrains.com/help/idea/how-to-use-wsl-development-environment-in-product.html

Maven构建找不到setttings.xml如何解决
错误类似下面的描述：
```bash
/home/dimitrda/apache-maven-3.8.4/bin/mvn -Dmaven.ext.class.path=/mnt/c/Users/dimitrda/UBS/Dev/opt/idea/plugins/maven/lib/maven-event-listener.jar test -s \\wsl$\wsl-fatty\home\dimitrda\philadelphia\settings.xml
[ERROR] Error executing Maven.
[ERROR] The specified user settings file does not exist: /home/dimitrda/philadelphia/\\wsl$\wsl-fatty\home\dimitrda\philadelphia\settings.xml
```
错误表现为：settings的路径前面多了项目的路径：/home/dimitrda/philadelphia/

https://youtrack.jetbrains.com/issue/IDEA-292386/Maven-in-WSL-fails-if-custom-settings.xml-is-picked-with-the-file-picker
