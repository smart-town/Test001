遍历Array可以采用下标循环，遍历Map和Set无法使用下标。为了统一集合类型，ES6引入新的 **iterable**类型，Array、Map和Set都属于 iterable 类型。

具有 iterable 类型的集合可以通过新的 **for ... of**循环遍历。该语法是 ES6 新引入的。
```
var a = ['A','B','C']
var m = new Map([[1,'x'],['2','y']]);
for(var x of a){
    console.log(x) ;
}
for(var x of m){
    console.log(x) ;
}
```

关于 **for...in**和 **for...of** 的区别
for...in 遍历的实际上是对象的属性名称，一个Array数组实际上也是一个对象，它的每个元素的索引被视为一个属性。
```
var a = ['A','B','C']
a.name = 'hello'
for(var x in a){
    console.log(x);//0,1,2,name
}
```
而for...of只循环集合本身元素。

另外，**forEach()**其实是更好的方式。它是iterable内置的forEach方法。它接口一个函数，每次迭代就自动回调该函数。