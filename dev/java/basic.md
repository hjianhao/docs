# 数据类型

## 数组

### 创建数组


1. 创建数组然后赋值
``` java
int data[] = new int[3]; /*开辟了一个长度为3的数组*/
data[0] = 10; // 第一个元素
data[1] = 20; // 第二个元素
data[2] = 30; // 第三个元素
```

2. 静态初始化
``` java
int data[] = {1, 2, 4, 545, 11, 32, 13131, 4444};    
```

### 输出数组

1. 传统for循环

2. for each循环
``` java
for(int a:array)
    System.out.println(a);
```
3. 利用Array类中的toString方法
``` java
System.out.println(Arrays.toString(array));
```

### byte数组

byte数组清零
``` java
java.util.Arrays.fill(bytes, (byte) 0)
```

## 数据类型转换

### 整型和字符串类型转换

整型转字符串的方法：

1. num + ""

2. String.valueOf(num)

3. Integer.toString(num)

方法1最慢应该避免使用，方法2，3差不多，方法2稍快。

参考10万次转换耗时比例：
num + "" : 45
String.valueOf(num) : 8
Integer.toString(num) : 9