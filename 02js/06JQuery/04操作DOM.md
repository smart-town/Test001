前面是通过jQuery的选择器来获得jQuery对象的，拿到之后有什么用呢？当然是用来操作DOM。
使用js修改DOM的css、文本、设置HTML时，有的浏览器只有innerHTML、有的浏览器支持innerText，有了jQuery对象就不用考虑这些浏览器的差异了。全都是统一操作。
## 修改Text和HTML
jQuery对象的<code>text()</code>和<code>html</code>方法分别获取节点的文本和原始HTML文本。
```
<ul id="test-ul">
    <li class="js">JavaScript</li>
    <li name="book">Java &amp; JavaScript</li>
</ul>

分别获取文本和HTML：
$('#test-ul li[name=book]').text() ;//Java & JavaScript
$('#test-ul li[name=book]').html() ;//Java &amp;JavaScript
```
**设置**文本或HTML，jQuery的API十分巧妙：无参调用text()是获取文本，传入参数就是设置文本。
```
var j1 = $('#test-ul li.js') ;
j1.html('<span style="color:red">JavaScript</span>') ;
```
注意
一个jQuery对象可能包含0个或任意个DOM对象，它的方法实际上会作用在对应的每个DOM节点上。所以jQuery对象的另一个好处是我们可以执行一个操作作用在对应的一组DOM节点上。即使选择器没有返回任何DOM节点，调用jQuery对象的方法仍然不会报错。这意味着jQuery帮我们省去了很多的if语句。

## 修改CSS
jQuery对象具有批量操作的特点，这用于修改css十分方便。
```
<ul id="test-css">
    <li class="lang dy"><span>JavaScript</span></li>
    <li class="lang"><span>Java</span></li>
    <li class="lang dy"><span>Python</span></li>
    <li class="lang"><span>Swift</span></li>
    <li class="lang dy"><span>Schema</span></li>
</ul>

```
要高亮动态语言，调用jQuery对象的<code>css()</code>方法即可
```
$('#test-css li.dy>span').css('background-color', '#ffd351').css('color', 'red') ;
```
**注意**，jQuery对象的所有方法都会返回一个jQuery对象，可能是新的也可能是自身。这样我们可以进行**链式调用**，非常方便。
**<code>css()</code>**方法可以这样用：
```
var div=$('#test-div') ;
div.css('color') ;//获取css属性
div.css('color', '#336699') ;//设置css属性
div.css('color', '') ; //清除color属性
```
为了保持和js一致，css属性可以用'background-color'和'backgroundColor'两种格式。
css()方法将作用于DOM节点的style属性，具有最高优先级。如果要修改**class**属性可以用jQuery提供的以下方法：
```
var div = $('#test-div') ;
div.hasClass('highlight') ;//class是否包含highlight
div.addClass('highlight') ;//添加这个class
div.removeClass('highlight') ;//删除
```
## 显示和隐藏DOM
要隐藏一个DOM，我们可以设置css的display属性为none，利用css()方法就可以实现。但是要显示这个DOM就需要恢复原有的display属性，这就得记下来原有的display属性到底是block还是inline还是别的值。考虑到显示和隐藏DOM元素非常普遍，jQuery直接提供了<code>show()</code>和<code>hide()</code>方法。
```
var a = $('a[target=_blank]') ;
a.hide() ;
a.show() ;
```
**注意**隐藏DOM节点并未改变DOM树的结构，它只影响DOM节点的显示。和删除是不一样的。
## 获取DOM信息
利用jQuery对象的若干方法，我们可以直接获取DOM的高宽等信息，无需针对不同的浏览器编写特定代码：
```
//浏览器可视窗口大小
$(window).width() ;
$(window).height() ;

//HTML文档大小
$(document).width() ;
$(document).height() ;

//某个div的大小
var div = $('#test-div') ;
div.width() ;
div.height() ;
div.width(400) ; //设置css属性 width:400px，是否生效要看css是否有效
div.height('200px') ; 
```
<code>attr()</code>和<code>reomveAttr()</code>方法用来操作DOM节点的属性。
```
//<div id="test-div" name="Test" start="1">
var div = $('#test-div') ;
div.attr('data') ; //undefined 属性不存在
div.attr('name') ;//'Test'
div.attr('name', 'Hello') ;//设置name属性
div.removeAttr('name') ;//删除name属性
div.attr('name') ;//undefined
```
<code>prop()</code>和<code>attr()</code>类似，但是HTML5规定有一种属性可以在DOM节点中没有值，只有出现和不出现两种，如：
```
<input id="test-radio" type="radio" name="test" checked value="1">
等价于
<input id="test-radio" type="radio" name="test" checked="checked" value="1">
```
attr()和prop()对于属性checked的处理略有不同
```
var radio = $('#test-radio') ;
radio.attr('checked') ;//'checked'
radio.prop('checked') ;//true

prop()的返回值更合理一些。但是用is()方法判断更好
radio.is(':checked') ;//true
```
类似的属性还有<code>selected</code>。处理时也最好用<code>is(':selected')</code>

## 操作表单
对于表单元素，jQuery对象统一提供<code>val()</code>方法获取和设置对应的value属性。
```
<input id="test-input" name="email" value=""/>
<select id="test-select" name="city">
    <option value="BJ" selected>BeiJing</option>
    <option value="SH">Shanghai</option>
    <option value="SZ">ShenZhen</option>
</select>
<textarea id="test-textarea">Hello</textarea>

var input = $('#test-input'), select = $('#test-select'), textarea=$('#test-textarea') ;
input.val() ;
input.val('hhg') ;

select.val() ;
select.val('SH') ;

textarea.val() ;
textarea.val('Hi') ;
```
可以看到一个val就统一了各种输入框的取值和赋值问题。
