---
title: JavaScript中的赋值、浅拷贝和深拷贝
tags:
  - JavaScript
  - Copy
date: 2021-02-01 23:38:35
updated: 2021-02-01 23:38:35
categories: [JavaScript]
---

前两天用对Vue做了一个页面，内用是表格，点击表格的编辑按钮弹出模态框可以进行编辑。  

|操作|姓名|性别|出生日期|身份证号|data|
|--|--|--|--|--|--|
|<font color="#3273dc">编辑</font>|张三|男|1970-01-01|01234567|data[0]|
|<font color="#3273dc">编辑</font>|李四|男|1980-01-01|12345670|data[1]|
|<font color="#3273dc">编辑</font>|王五|女|1990-01-01|23456701|data[2]|

点击编辑后弹出模态框  
将data[0]赋值给person

```
let person = data[0]
```
|person||
|--|--|
|姓名|张三|
|性别|男|
|出生日期|1970-01-01|
|身份证号|0123457|

编辑后弹窗的内容发现表格上的内容也会同步改变，难道是法外狂徒在捣鬼？  

***

首先让我们回顾一下JavaScript的数据类型

## JavaScript的数据类型

JavaScrit中共有7种基本数据类型（原始数据类型）和对象  

- 基本类型
    - `Undefined`
    - `Null`
    - `Boolean`
    - `String`
    - `Symbol`
    - Numeric
        - `Number`
        - `BigInt`
- 对象类型
    - `Object`
    - `Funcion`
    - `Date`
    - `JSON`
    - `Array`
    - `Map`
    - ......  

JavaScript中所有的基本类型只能替换，不能改变。

## `Assignment`赋值

```
let x = 3;
let y = x;
y + 1;

console.log(x);
console.log(y);
// expected output: 3
// expected output: 4


let data = [{ name: 'zhangsan', sex: 1 }];
let person = data[0];
person.name = 'wangwu';

console.log(data[0]);
// expected output: { name: 'wangwu', sex: 1 }

```
这是因为虽然`data[0]`和`person`的名称不同，但是却指向了同一个内存地址。  

|变量||内存地址||内存空间|
|--|--|--|--|--|
|data[0]<br>person|&rarr;<br>&rarr;|0x11FF00|&rarr;|{ name: 'wangwu', sex: 1 }|  

data和person既不是相同的变量名也不是相同的数据类型，但是data[0]和person他们指向了相同的内存地址，所以改变person时，data[0]也会同步改变。  
那如何只改变person而不改变data[0]呢？我们把两个data[0]复制一份给person让他们存储在不同的内存块中不就可以了，这就是拷贝。拷贝分为浅拷贝和深拷贝。

## 浅拷贝(Shallow Copy)

浅拷贝是指将对象内的原始数据类型复制一份，引用类型复制内存地址。  
我们新建一个对象

```
let obj1 = {srt:'str' , arr:[1,2,3]}
```

拷贝前  

|obj||内存区域A||内存区域B|
|--|--|--|--|--|
|obj1.str|&rarr;|'str'|||
|obj1.arr|&rarr;|0x11FF00|&rarr;|[1,2,3]|  

将`obj1`浅拷贝之到`obj2`之后  

|obj||内存区域A||内存区域B|
|--|--|--|--|--|
|obj1.str|&rarr;|'str'|||
|obj1.arr|&rarr;|0x11FF00|&rarr;|[1,2,3]|
|||||
|obj2.str|&rarr;|'str'|||
|obj2.str|&rarr;|0x11FF00|↗||

### 浅拷贝的方式  

#### 1.`Oject.assign()`

`Object.assign()`可以将一个或多个源对象拷贝给目标对象并返回目标对象  
语法
```
Object.assign(target, sources)
```

#### 2.展开语法(Spread syntax)

- 对象拷贝  

```
let objClone = { ...obj };
```

- 数组拷贝  

```
let newArr = [...oldArr];
```

#### 3.数组分割`Array.prototype.slice()`  

```
let newArray = oldArray.slice([begin[, end]])
```

#### 4. 数组合并`Array.prototype.concat()`

```
let newArray = oldArray.concat([value1[, ...[, valueN]]])
```

等同于

```
let newArray = [...oldArray, ...value1, ...]
```

数组的分割和合并首先会创建一个新的数组，所以也就对原来的数组完成了拷贝。

## 深拷贝(Deep Copy)

深拷贝将是将原来的对象递归赋值，首先创建一个新的对象，然后将原来对象的内容递归复制到新的对象，原对象的引用类型也会复制一份。  

#### 1. JSON.parse(JSON.stringify(object))

```
let newObj = JSON.parse(JSON.stringify(oldObj))
```

缺点:  
- `undefinde`、`function`、`symbol` 在序列化过程中会被忽略
- `Date`在序列化过程中会被转为字符串（调用了toJSON())
- NaN 和 Infinity 格式的数值及 null 都会被当做 null
- 循环引用的对象会形成无限循环然后报错  

#### 2. Node.js利用v8引擎的序列化和反序列化接口

```
const v8 = require('v8');
let newObj = v8.deserialize(v8.serialize(oldObj))
```

#### 3.第三方JS库

- [Lodash](https://lodash.com/docs#cloneDeep)

```
lodash.clonedeep(objects)
```

- [AngularJS](https://docs.angularjs.org/api/ng/function/angular.copy)

```
angular.copy(source, [destination])
```

- [jQuery](https://api.jquery.com/jquery.extend/#jQuery-extend-deep-target-object1-objectN)

```
jQuery.extend( true, target, object1 [, objectN ] )
```

## 参考

1.  [JavaScript data types and data structures | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)
1. [ECMA-262](https://tc39.es/ecma262/)
1. [Spread syntax (...) | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
1. [What is the most efficient way to deep clone an object in JavaScript? - Stack Overflow](https://stackoverflow.com/questions/122102/what-is-the-most-efficient-way-to-deep-clone-an-object-in-javascript/10916838#10916838)