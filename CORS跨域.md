## CORS跨域

简介：CORS是一个W3C标准，全称是"跨域资源共享"（Cross-origin resource sharing）

它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制

CORS需要浏览器和服务器同时支持（IE10+）

对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样

浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息 （有时还会多出一次附加的预检请求）

实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。


**两种请求**

浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）  
浏览器对这两种请求的处理，是不一样的

**简单请求**

满足一下两大条件：  
（1）请求方法是 HEAD、GET、POST 三种方法中的其中一种
（2）HTTP的头信息不超出以下几种字段：  
  * Accept
  * Accept-Language
  * Content-Language
  * Last-Event-ID
  * Content-Type 只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain

对于简单请求，浏览器直接发出CORS请求。具体来说，就是在请求报文的头信息之中，增加一个Origin字段  
Origin字段用来说明，本次请求来自哪个源（协议 + 域名 + 端口）  
服务器根据这个值，决定是否同意这次请求  

【如果Origin指定的源，不在许可范围内】  
服务器会返回一个正常的HTTP回应  
但浏览器发现，这个响应报文的头信息没有包含Access-Control-Allow-Origin字段，会抛出一个错误  

【如果Origin指定的源，在许可范围内】  
服务器返回的响应报文中会多出几个头信息字段：  
```
// Response Headers
Access-Control-Allow-Credentials:true （后台设置允许接收cookie）
Access-Control-Allow-Headers:X-Requested-With,X-Auth-Token,Content-Type （后台设置允许的头部）
Access-Control-Allow-Methods:POST,PUT,GET,OPTIONS,DELETE,HEAD,PATCH （后台设置允许的请求方式）
Access-Control-Allow-Origin:192.168.1.118 （后台设置允许的源）
```
后台设置操作  
`Access-Control-Allow-Origin` 该字段必须，要么是请求时Origin字段的值，要么是*（接收任意域名的请求）  
`Access-Control-Allow-Credentials` 该字段可选，后台如果需要cookie里的信息作为登录判断，可将它的值设为true（允许接收cookie）  
`Access-Control-Expose-Headers` 该字段可选，CORS请求时，XMLHttpRequest对象的getResponseHeader()方法只能获取6个基本字段
    Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma。  
    如果想拿到其他字段，就必须在该字段里面指定。getResponseHeader('X-Requested-With')可以返回X-Requested-With字段的值。

**非简单请求**

不同时满足上面两个条件，就属于非简单请求

例如：请求的方法是PUT或DELETE或者Content-Type字段类型是application/json  

非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"请求（preflight）

预检的作用：浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。

只有得到肯定答复，浏览器才会发出正式的XMLHttpRequest请求，否则就报错。

"预检"请求用的请求方法是OPTIONS，表示这个请求是用来询问的  

```
// Ajax
var url = 请求服务器域名地址;
var xhr = new XMLHttpRequest();
xhr.open('POST', url, true);
xhr.setRequestHeader('X-Custom-Header', 'value');
xhr.send();

// 预检请求头信息
Origin: 当前浏览器所在域名
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: X-Custom-Header
Host: 请求服务器域名

// 预检请求通过后的回应
Access-Control-Allow-Origin: 当前浏览器所在域名
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
```

**withCredentials 属性**  
CORS请求默认不发送Cookie和HTTP认证信息。  
如果要把Cookie发到服务器  
一方面要服务器同意，后台指定Access-Control-Allow-Credentials字段`Access-Control-Allow-Credentials: true`  
另一方面要Ajax请求中打开withCredentials属性`xhr.withCredentials = true`  
否则，即使服务器同意接收Cookie，浏览器也不会发送。（携带cookie是一个双向的行为，两边都需要同意）  
如果省略withCredentials设置，有的浏览器还是会一起发送Cookie。这时，可以显式关闭`xhr.withCredentials = false`

!!! 注意：如果要发送Cookie，Access-Control-Allow-Origin就不能设为星号，必须指定明确的、与请求网页一致的域名  
Cookie依然遵循同源政策，只有用服务器域名设置的Cookie才会上传，其他域名的Cookie并不会上传，且（跨源）原网页代码中的document.cookie也无法读取服务器域名下的Cookie。