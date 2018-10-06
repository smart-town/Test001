# 一、Canvas简介
canvas是h5新增的，一个可以使用脚本（通常是js）在其中绘制图像的HTML元素，它可以用来制作照片集或者制作简单的动画。甚至可以进行实时的视频处理和渲染。
它最初由苹果内部使用自己MacOS X WebKit推出，供应用程序使用像仪表盘的构件和Safari浏览器使用，后来，有人通过Gecko内核的浏览器（尤其是Mozilla和Firefox），Opera和Chrome和超文本网络应用技术工作组建议为下一代的网络技术使用该技术。
Canvas是由HTML代码配合高度和宽度属性而定义出的可绘制区域。js代码可以访问该区域，类似于其他通用的二维API，通过一套完整的绘图函数来动态生成图形。

## 二、基本使用
### 1.\<Canvas\>元素
```
<canvas id="tutorial" width="300" height="300"></canvas>
```
canvas看起来和img标签一样，只是canvas只有两个可选的属性width、height属性。而没有src、alt属性。
如果不给canvas设置width、height属性，则默认width为300、heigth为150，单位都是px。也可以使用css属性来设置宽高，但是如果宽和高的初始比例不一样，它会出现扭曲。所以建议永远不要用css属性来设置canvas的宽高。
#### 替换内容
由于某些较老的浏览器或者浏览器不支持HTML元素canvas，在这些浏览器上总能展示替代内容。
支持canvas的浏览器只会渲染canvas标签，而忽略其中的替代内容。不支持canvas的浏览器则会直接渲染内容取代。
```
<canvas>
    你的浏览器不支持canvas请升级
</canvas>
```
### 1.渲染上下文
canvas会创建一个固定大小的画布，会公开一个或者多个**渲染上下文（画笔）**，使用**渲染上下文**来绘制和处理要展示的内容。重点研究2D渲染上下文。其他的上下文暂不关注。
```
var canvas = document.getElementById("tutorial") ;
var ctx = canvas.getContext("2d") ;
```
### 2.检测支持性
```
var canvas = document.getElementById("tutorial") ;
if(canvas.getContext){
    var ctx = canvas.getContext("2d") ;
}else{
    //un-support 
}
```
## 绘制形状
### 1.栅格和坐标空间
canvas元素默认被网格所覆盖，通常来说网格中的一个单元相当于canvas元素中的一个像素。栅格的起点为左上角(0,0)，所有元素的坐标都相对于原点来定位。
### 2.绘制矩形
canvas只支持一种原生的图形绘制：矩形。所有其他图形都至少需要生成一种路径。但是，我们拥有众多路径的生成方法，让复杂图形的绘制称为了可能。**canvas提供了三种方法绘制矩形**：
- fillRect(x,y, width, height) ; //绘制一个填充的矩形
- strokeRect(x, y, width, height); //绘制一个矩形的边框
- clearRect(x, y, width, height);清除指定的矩形区域，然后这块区域会变得完全透明。
这三个方法具有相同参数，x,y为矩形的左上角坐标。width和height指的是绘制矩形的宽和高。
```
function draw(){
    var canvas = document.getElementById("hhg") ;
    if(!canvas.getContext) return ;
    var ctx = canvas.getContext() ;
    ctx.fillRect(10,10,100,50) ;
    ctx.strokeRect(10, 70, 100, 50) ;
}
draw() ;
```
### 3.绘制路径
图形的基本元素是路径。
路径是通过不同颜色和宽度的线段或曲线相连形成的不同形状的点的集合。
一个路径、甚至一个子路径，都是闭合的。

使用路径绘图需要一定的额外步骤：
1. 创建路径起始点
2. 调用绘制方法去绘制出路径
3. 把路径封闭
4. 一旦路径生成，通过描边或者填充路径区域来渲染图形

下面是需要用到的方法：
1. beginPath() : 创建一条路径，路径一旦建立成功，图形绘制命令被指引到路径上生成路径。
2. moveTo(x,y) : 把画笔移动到指定的坐标(x,y)，相当于设置路径的起点坐标
3. closePath() ：闭合路径之后，图形绘制命令重新指引到上下文中
4. stroke() : 通过线条来绘制图形轮廓
5. fill() : 通过填充路径的内容区域生成实心的图形

**绘制线段**
```
var canvas = document.getElementById("tutorial") ;
if(!canvas.getContext()) return ;
var ctx = canvas.getContext("2d") ;
ctx.beginPath() ;//新建一条path
ctx.moveTo(50,50) ;//将画笔移动到指定坐标
ctx.lineTo(200,500) ;//绘制一条从当前位置到指定坐标的值
ctx.closePath() ;//闭合路径，拉一条从当前点到path起始点的直线，如果当前点与起始点重合，则什么都不做
ctx.stroke() ;//绘制路径
```
**绘制三角形边框**
```
var canvas = document.getElementById("tutorial") ;
if(!canvas.getContext()) return ;
var ctx = canvas.getContext("2d") ;
ctx.beginPath() ;
ctx.moveTo(50,50) ;
ctx.lineTo(200,50) ;
ctx.lineTo(200,200) ;
ctx.closePath() ;//虽然只画了两条线段但是closePath会闭合，这样会生成一个三角形
ctx.stroke() ; //描边，stroke不会自动closePath() ;

ctx.fill() ;//填充闭合区域，如果path没有闭合，则fill会自动闭合路径
```
**绘制圆弧**
两个方法可以绘制圆弧：
1. arc(x, y, r, startAngle, endAngle, anticlockwise) ;以x,y为圆心，以r为半径，从startAngle到endArgle弧度结束，anticlockwise是布尔值，true表示逆时针，否则是顺时针。默认为顺时针
2. arcTo(x1,y1,x2,y2,radius)，根据给定的控制点和半径画一段圆弧，最后再以直线连接两个控制点
**实例**
```
var canvas = document.getElementById("tutorial") ;
if(!canvas.getContext) return ;
var ctx = canvas.getContext("2d") ;
ctx.beginPath() ;
ctx.arc(50,50,40,0,Math.PI/2,false) ;
ctx.stroke() ;
```