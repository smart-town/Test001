js中实现对象在 ES6 之前，是基于原型实现的。特点是简单但是缺点在于理解起来比传统的类——实例模型困难。最大缺点在于继承的实现要编写大量代码，并且需要正确实现原型链。

ES6中有了新的关键字**class**，其目的就是让定义类更简单。
用函数实现 Student 的方法：
```
function Student(name){
    this.name = name ;
}
Student.prototype.hello = function(){
    alert("hello," + this.name + "!") ;
}
```
如果用 class 关键字：
```
class Student{
    constructor(name){
        this.name = name ;
    }

    hello(){
        alert("hello," + this.name) ;
    }
}
```
比较一下可以发现，class 的定义包含了构造函数 constructor 和定义在原型对象上的函数 hello()(注意没有function关键字)。这样就避免了 Student.prototype.hello = function(){...} 这样分散的代码。

## class继承
用 class 定义对象的另一好处就是继承更方便了。之前从 Student 中派生出一个 PrimaryStudent 需要编写很多代码，而现在，原型继承的中间对象，原型对象的构造函数等都不需要考虑了。直接通过extends实现。
```
class PrimaryStudent extends Student{
    constructor(name,grade){
        super(name) ;//super调用父类构造方法
        this.grade = grade ;
    }
    myGrade(){
        alert("i am grade ' + this.grade) ;
    }
}
```
注意 PrimaryStudent 的定义也是 class 关键字实现的。而 extends 则表示原型链对象来自 Student。子类的构造函数可能会和父类不太相同。例如上面的 PrimaryStudent 需要 name 和 grade 两个参数，并且需要通过 super(name) 来调用父类的构造函数，否则父类的 name 属性无法正常初始化。

ES6 引入的 class 和原有的原型继承有什么区别呢？实际上它们没有任何区别。class的作用就是让js引擎去实现原来我们要自己编写的原型链代码。简而言之，用 class 的好处就是极大简化了原型链代码。 

**但是**，现在并不是所有的主流浏览器都支持，如果一定要用，则要用到 **Babel**这个工具将其转换为传统的 prototype 代码。