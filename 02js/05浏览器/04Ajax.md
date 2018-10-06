AJAX并不是js的语言规范，它是Asynchronous JavaScript and XML，即使用js执行异步网络请求。

如果仔细观察一个Form的提交，一旦用户点击submit按钮，表单开始提交，浏览器就会刷新页面，然后在新页面里告诉操作成功还是失败，但是如果由于网络失败或其他原因，就只能得到一个 404 页面。

即Web的运作是：一次HTTP请求对应一个页面

如果让用户停留在当前的页面，同时发出新的HTTP请求，就必须用js发出请求，接收到数据后，再用js更新页面，这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。

最早大规模使用AJAX的就是Gmail，Gmail的页面在首次加载后，剩下的内容都是依赖于AJAX更新。

用js编写一个完整的ajax代码并不复杂，但是要注意：ajax是异步执行的，也就是说，要通过回调函数获得响应。

在现代浏览器上Ajax主要是通过XMLHttpRequest对象
```
function success(test){
    var test = document.getElementById("test-response") ;
    test.value = test ;
}
function fail(code){
    var test = document.getElementById("test-response") ;
    test.value = code ; 
}
var request  = new XMLHttpRequest() ;
request.onreadystatechange = function(){
    if(request.readyState === 4){
        if(request.status === 200){
            return success(request.responseText) ;
        }
        else {
            return fail(request.status) ;
        }
    }
}
```
对于低版本的IE，需要换一个ActiveXObject对象。
将两者统一起来的写法：
```
var request ;
if(window.XMLHttpRequest){
    request = new XMLHttpRequest() ;
} else {
    request = new ActiveXObject("Microsoft.XMLHTTP") ;
}
```
通过检测window对象是否有XMLHttpRequest属性来确定浏览器是否支持标准的XMLHttpRequest。注意不要根据浏览器的navigator.userAgent来检测浏览器是否支持js某个特性，一是因为这个字符串本身可以伪造，二是通过ie版本判断js特性非常复杂。
当创建了 XMLHttpRequest 对象后，要先设置 onreadystatechange 回调函数，在回调函数中，通过我们只需要通过 readyState===4判断请求是否已经完成，如果已经完成，则再根据status===200判断是否是一个成功的响应。
XMLHttpRequest 对象的 open() 方法有 3 个参数，第一个参数指定是 GET 还是 POST，第二个参数指定URL地址，第三个参数指定是否使用异步，默认为true，所以不写。
**注意**，千万不要把第三个参数指定为false，否则浏览器为同步响应，直到ajax请求完成。
最后调用 send() 方法才算是真正发送请求，GET请求不需要参数，POST请求需要把body部分以字符串或者FormData对象传进去。
## 安全限制
默认情况下，js在发送ajax请求时，URL的域名必须和当前页面完全一致。完全一致的意思是，域名要相同。(www.example.com和example.com)就是不同的。协议要相同(http和https不同)，端口号要相同(默认是:80端口，和:8080不同)。大多数浏览器都会严格遵循这个限制。

那是不是用js就不能请求外域的URL了呢？方法还是有的，大概有以下几种：
- 通过flash插件发送请求，这种方式可以绕过浏览器的安全限制，但是必须安装flash，并且跟flash交互，不过flash用起来很麻烦，而且现在也用的越来越少了
- 通过在同源域名下假设一个代理服务器来转发，js负责将请求发送到代理服务器，代理服务器再把结果返回。这样就遵循了浏览器的同源策略，这种方式的麻烦之处在于需要服务器端额外做开发
- JSONP，有个限制，只能用 GET 请求，并且要求返回 javascript，这种方式实际上是利用了浏览器允许跨域引用js资源:
    ```
    <head><script src="http://example.com/x.js"></script></head>
    ```
    JSONP通常以函数调用的形式返回，如：返回的js内容为foo('data')，这样一来，我们如果在页面中先准备好foo()函数，然后给页面动态加一个script节点，相当于动态读取外域的js资源，最后就等着接收回调了。
- CORS。如果浏览器支持H5，就可以一劳永逸地使用新的跨域策略了：CORS。全称为Access-Control-Allow-Origin，是h5规范定义的如何跨域访问资源。Origin表示本域，也就是浏览器当前页面的域，当js向外域发起请求后，浏览器收到响应后，首先检查Access-Control-Allow-Origin是否包含本域，如果是，则此次跨域请求成功，如果不是则请求失败，js将无法获取到响应的任何数据。

    假设本域是my.com，外域是sina.com，只要响应头Access-Control-Allow-Origin为http://my.com或者\*，本次请求就可以成功。可见跨域能够成功主要取决于对方服务器是否愿意给你设置一个正确的Access-Control-Allow-Origin，决定权始终在对方手中。

无论是否用到JavaScript通过CORS跨域请求资源，都要了解CORS的原理。最新的浏览器全面支持HTML5，在引用外域资源的时候，除了js和css外，都要验证CORS，如当你引用了某个第三方的字体文件时，但是该第三方并未正确设置Access-Control-Allow-Origin，那么浏览器就无法正确加载字体。对于PUT、DELETE以及其他类型如application/json的POST请求，在发送AJAX请求之前，浏览器会先发送一个OPTIONS请求到这个URL上，询问对方是否接受。