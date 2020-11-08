
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [概念](#概念)
  - [对象](#对象)
    - [创建对象](#创建对象)
      - [对象直接量](#对象直接量)
      - [通过new创建对象](#通过new创建对象)
      - [原型](#原型)
  - [属性](#属性)

<!-- /code_chunk_output -->



# 概念

## 对象
对象是一种复合值：它将很多值（原始值或者其他对象）聚合在一起，可通过名字访问这些值。

JavaScript对象是动态的——可以新增属性也可以删除属性——但它们常用来模拟静态对象以及静态类型语言中的“结构体”（struct）。

对象最常见的用法是创建（create）、设置（set）、查找（query）、删除（delete）、检测（test）和枚举（enumerate）它的属性

每个对象还拥有三个相关的对象特性（object attribute）：
- 对象的原型（prototype）指向另外一个对象，本对象的属性继承自它的原型对象。
- 对象的类（class）是一个标识对象类型的字符串。
- 对象的扩展标记（extensible flag）指明了（在ECMAScript 5中）是否可以向该对象添加新属性。

对象分类：
- 内置对象（native object）是由ECMAScript规范定义的对象或类。例如，数组、函数、日期和正则表达式都是内置对象。
- 宿主对象（host object）是由JavaScript解释器所嵌入的宿主环境（比如Web浏览器）定义的。客户端JavaScript中表示网页结构的HTMLElement对象均是宿主对象。既然宿主环境定义的方法可以当成普通的JavaScript函数对象，那么宿主对象也可以当成内置对象。
- 自定义对象（user-defined object）是由运行中的JavaScript代码创建的对象。

### 创建对象

创建对象的方式:
1. 对象直接量
2. 通过new创建对象
3. 原型

#### 对象直接量

对象直接量是由若干名/值对组成的映射表，名/值对中间用冒号分隔，名/值对之间用逗号分隔，整个映射表用花括号括起来。

``` javascript
var empty = {}; // 没有任何属性的对象
var point = { x:0, y:0};  // 有两个属性
var point2 = { x:point.x, y:point.y + 1}; // 更复杂的值
var book = {
    "main title" : "JavaScript",    // 属性名有空格，必须用字符串表示
    "sub-title" : "The Definitive Guide", // 属性名里有'-'，必须用字符串表示
    "for" : "All audience", // "for"是保留字，必须用字符串表示
    author : {
        firstname : "David",
        surname : "Flanagan"
    }
};

```

#### 通过new创建对象

new运算符创建并初始化一个新对象。关键字new后跟随一个函数调用。这里的函数称做构造函数（constructor），构造函数用以初始化一个新创建的对象。

```javascript
var o = new Object (); // 创建一个空对象，和{}一样
var a = new Array (); // 创建一个空数组，和[]一样
var d = new Date (); // 创建一个Date对象
var r = new RegExp ("js"); // 创建一个模式匹配对象
```

#### 原型

``` javascript
var o1 = Object.create ({x : 1, y : 2}); // o1继承了x, y属性
var o2 = Object.create (null); // o2不继承任何属性和方法，包括toString()等基础方法，不能和'+'符号一起工作
var o3 = Object.create (Object.prototype); // 和{}与new Object () 一致
```


## 属性

属性包括名字和值。

属性名可以是包含空字符串在内的任意字符串，但对象中不能存在两个同名的属性。

值可以是:
- 任意JavaScript值
- 在ECMAScript 5中可以是一个getter或setter函数（或两者都有）。

每个属性还有一些与之相关的值，称为“属性特性”（property attribute）
- 可写（writable attribute），表明是否可以设置该属性的值。
- 可枚举（enumerable attribute），表明是否可以通过for/in循环返回该属性。
- 可配置（configurable attribute），表明是否可以删除或修改该属性。

属性分类：
- 自有属性（own property）是直接在对象中定义的属性。
- 继承属性（inherited property）是在对象的原型对象中定义的属性。