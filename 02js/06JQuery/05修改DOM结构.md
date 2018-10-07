直接使用浏览器提供的API对DOM结构进行修改，不但代码复杂而且针对不同的浏览器要写不同的代码。而有了jQuery我们只要专注于jQuery本身就可以了。底层的DOM操作由jQuery完成就可以了。
## 添加DOM
要添加新的DOM节点，除了通过jQuery的<code>html()</code>这种暴力方法外，还可以用<code>append</code>方法。如：
```
<div id="test-div">
    <ul>
        <li><span>JavaScript</span></li>
        <li><span>Python<span></li>
        <li><span>Swift</span></li>
    </ul>
</div>

向列表新添加一个节点：
var ul = $('#test-div>ul') ;
ul.append('<li><span>Hashkell</span></li>') ;
```
除了接受字符串，append还可以传入原始的DOM对象、jQuery对象和函数对象
```
//创建DOM对象
var ps = document.createElement('li') ;
ps.innerHTML = '<span>Pascal</span>' ;
//添加DOM对象
ul.append(ps) ;

//添加jQuery对象
ul.append($('#schema')) ;

//添加函数对象
ul.append(function(index,html){
    return '<li><span>Language-' + index + '</span></li>' ;
});
```
传入函数时，要求返回一个字符串、DOM对象或者jQuery对象。因为jQuery的append()可能作用于**一组DOM节点**，只有**传入函数才能针对每个DOM生成不同的节点**。
<code>append()</code>把DOM添加到最后，<code>preend()</code>则把DOM添加到最前面
另外还需要注意，如果要添加的DOM节点已经存在于HTML文档中，它会首先从文档中移除，然后再添加。也就是说，用append()可以移动一个DOM节点。
如果要把新节点插入到指定位置，例如js和python之间，那么可以先定位到JavaScript，然后调用<code>after</code>方法。
```
var js = $('#test-div>ul>li:first-child');
js.after('<li><span>Lua</span></li>') ;
```
也就是说，同级节点可以使用<code>after()</code>或者<code>before()</code>方法
## 删除节点
要删除DOM节点，拿到jQuery对象后直接调用remove()方法就可以了。如果jQuery对象包含若干DOM节点，实际上可以一次删除多个DOM节点。
```
var li = $('#test-div>ul>li') ;
li.remove() ;//所有<li>都被删除
```