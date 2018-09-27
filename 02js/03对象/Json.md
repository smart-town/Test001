JSON 即 javascript object notation 缩写。它是一种**数据交换格式**

在 JSON 出现之前， 大家一直使用 XML 来传递数据。因为 XML 是一种纯文本格式，所以其适合在网络上交换数据。其本身并不复杂但是加上DTD、XSD等一大堆规范后，就很麻烦了。后来在2002年某一天，道格拉斯·克罗克夫特发明了JSON这种超轻量级数据交换格式。他长期担任雅虎的高级架构师，钟情于js，他的json其实是JavaScript的一个子集。

JSON中一共就这几种数据类型：
- number
- boolean
- string
- null
- array : js中的Array表示方式——[]
- object : js中的{...}
以及上面的任意组合

并且 JSON 规定了字符集必须是 UTF-8.表示多语言就没有问题了。为了统一解析，JSON的字符串规定必须用双引号“”。Object的键也必须用双引号。由于JSON的简单很快就风靡Web世界并成为了ECMA标准。几乎所有语言都有JSON解析库。而在js中内置了JSON，可以直接使用。

把js任何对象变成 JSON，就是把这个对象**序列化**为一个JSON格式字符串，这样才能够通过网络传递到其他计算机
如果我们收到一个JSON格式字符串，只需要把它反序列化为一个JavaScript对象，就可以在JS中直接使用这个对象了。

## 序列化
```
'use strict';
var xiaoming={
    name: 'xiaoming',
    age: 14,
    grade: null,
    skills: ['js', 'java', 'python']
};
var s = JSON.stringify(xiaoming) ;
console.log(s) ;
```
要好看些可以加上参数，按照缩进输出：
```
JSON.stringify(xiaoming,null,'  ');
```
**第二个参数**用来控制如何筛选对象的键值，如果我们只想输出指定的属性，可以传入Array:
```
JSON.stringify(xiaoming, ['name','skills'], '  ');
```
还可以传入一个函数，这样每个对象的键值对都会先被函数处理：
```
function convert(key,value){
    if(typeof value === 'string'){
        return value.toUpperCase() ;
    }
    return value;
}
JSON.stringify(xiaoming, convert, '  ');
```
**精确控制如何序列化对象**，可以给对象定义一个 toJSON() 方法，直接返回JSON应该序列化的数据：
```
var xiaoming = {
    name: 'xiaoming',
    age: 14,
    toJSON: function(){
        return {'NAME': this.name};
    }
};
```

## 反序列化
拿到一个JSON字符串，我们直接用**JSON.parse**将它变为一个js对象：
JSON.parse('[1,2,3,'true']');
JSON.parse还可以接收一个函数，用来转换解析出的属性。
