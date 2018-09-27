HTML被浏览器解析后就是一个DOM树，要改变HTML结构就要通过js来操作DOM。
对于一棵DOM树，操作一个DOM节点实际上就是这几个操作：
- 更新
- 遍历
- 添加
- 删除

## 获取节点
### 方法一
在操作一个DOM节点前，首先需要获取到这个节点。常用方法：
- document.getElementById():可以直接定位唯一的一个DOM节点
- document.getElementsByTagName()
- CSS 选择器：  document.getElementsByClassName()
### 方法二
使用 querySelector() 和 querySelectorAll()。需要了解 selector 语法

```
语法
document.querySelector(CSS selector)
参数为CSS选择器，指定一个或多个匹配元素的CSS选择器。可以使用：id、类、类型、属性、属性值，对于多个选择器使用逗号隔开。
document.querySelector("p"); //获取文档第一个 \<p\> 元素
document.querySelector(".example"); //class=example的第一个元素
document.querySelector("p.example");//第一个class=example的\<p\>元素
document.querySelector("a[target]"); //第一个有target属性的\<a\>元素


var q1 = document.querySelector('#q1') ; //通过querySelectory获取ID为q1的节点
var ps = q1.querySelectorAll('div.highlighted > p'); //通过该函数获取q1节点内符合条件的所有节点
```