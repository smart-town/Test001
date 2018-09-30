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
## 更新、插入、删除DOM
拿到一个DOM结点后，可以对它进行更新、插入、删除操作。
### 更新
直接修改节点的文本，两种方法
- innerHTML 属性，这个方式非常强大，不仅可以修改一个节点的文本内容，还可以直接通过HTML片段修改DOM节点内部的子树
    ```
    var p = document.getElementById("p-id") ;
    p.innerHTML = "ABC";
    p.innerHTML = "ABC<span style='color:red'>RED</span>XYZ" ;
    ```
用 innerHTML 时要注意是否要写入HTML，如果写入的字符串是通过网络拿到的，要注意对字符编码来避免XSS攻击。
- 第二种是修改**innerText或textContent**属性，这样可以自动对字符串进行HTML编码，保证无法设置任何HTML标签。两者的区别在于读取属性时，innerText不返回隐藏元素的文本，而textContent返回所有的文本。注意IE9不支持textContent
    ```
    p.innerText = '<script>alert("okk")</script>'; //HTML被自动编码，无法设置<script>节点
    ```
**修改CSS**也是经常需要的操作，DOM节点的style属性对应所有的css，可以获取或设置，因为css允许font-size这样的名称，但是它并非js中有效的属性名，所以需要在js中修改位**驼峰式**写法fontSize
```
p.style.color = "#ff0000" ;
p.style.fontSize = "20px" ;
```
### 插入
获得某个节点后，在这个节点内插入新的DOM。
如果这个节点是空的，那么可以直接使用innerHTML=...就可以修改DOM节点的内容，相当于插入了新的DOM节点。
但是如果节点不是空的，那么就不能这么做，因为innerHTML会直接替换掉原来所有的子结点。

有两个办法可以插入新的节点
- 使用 appendChild，将一个子结点添加到父结点的最后一个子结点。例如：
    ```
    <p id='js'>JavaScript</p>
    <div id='list'>
        <p id='java'>Java</p>
        <p id='python'>Python</p>
    </div>

    添加：
    var js = document.getElementById("js") ;
    var list = document.getElementById("list") ;
    list.appendChild(js) ;
    //由于我们插入的js结点已经存在于当前的文档树，所以这个节点会先从原位置删除再插入到新的位置
    ```
    更多的时候我们会从零创建一个新的结点，然后插入到指定位置
    ```
    var hask = document.createElement('p') ;
    hask.id = 'hask' ;
    hask.innerText = 'createElement test' ;
    list.appendChild(hask) ;
    ```
    这样就动态地增加了一个新的结点。动态创建一个节点然后添加到DOM树中可以实现很多功能，比如下面的例子中动态创建了一个style节点，然后将它添加到head的末尾，这样就动态地给文档添加了新的css定义：
    ```
    var d = document.createElement('style') ;
    d.setAttribute('type','text/css') ;
    d.innerHTML = 'p{color: red}' ;
    document.getElementsByTagName('head')[0].appendChild(d) ;
    ```
- insertBefore。将子节点插入到指定位置。可以使用**parentElement.insertBefore(newElement, referenceElement)**，子节点会插入到 referenceElement 之前。
    ```
    var list = document.getElementById('list') ;
    var ref = document.getElementById('python') ;
    var has = document.createElement('p') ;
    has.id = 'has' ;
    has.innerText = 'haskell' ;
    list.insertBefore(has, ref) ;
    ```
    可以看到使用insertBefore重点是拿到“参考子节点”的引用，很多时候需要循环一个父结点的所有子节点：
    ```
    var i, c ;
    var list = document.getElementById("list") ;
    for(i = 0; i < list.children.length; i++){
        c = list.children[i] ;
    }
    ```
## 删除
删除一个节点比插入容易很多，首先获得该节点以及它的父节点，然后调用父节点的 removeChild 进行删除。
```
var self = document.getElementById("will") ;
var parent = self.parentElement ;
var removed = parent.removeChild(self) ;
removed === self ;
```
但是需要注意的是，删除后的结点虽然不在文档树中了，但是其实还在内存中，可以随时再次被添加到别的位置。
当遍历一个父节点的子节点并进行删除操作时，要注意，**children**属性只是一个只读属性，并且它在子节点变化时会实时更新。
```
如对以下的HTML结构
<div id="parent">
    <p>First</p>
    <p>Second</p>
</div>

当使用如下代码删除时会报错：
var parent = document.getElementById("parent") ;
parent.removeChild(parent.children[0]) ;
parent.removeChild(parent.children[1]) ; //报错
原因在于，当第一个节被删除之后，children属性节点数量已经从2变成1了，索引1已经不存在了。
```
因此，删除多个节点时，要注意**children属性在时刻变化**.