

同源策略cross-domain policy：无法向其他网站发起ajax请求。


## 服务器中转

## jsonp: json with padding

浏览器绕开同源策略，发起跨域请求的一种方式。
因为同源策略限制，无法通过XMLHTTPRequest来向其他站点请求数据，
因此，我们通过一个<script>标签来发起请求，标签的source指向目标站点。

The following assumes a response object { "bar" : "baz" },

json:
```
var xhr = new XMLHttpRequest();

xhr.onreadystatechange = function () {
  if (xhr.readyState == 4 && xhr.status == 200) {
    document.getElementById("output").innerHTML = eval('(' + this.responseText + ')').bar;
  };
};

xhr.open("GET", "somewhere.php", true);
xhr.send();
```


jsonp:
```
function foo(response) {
  document.getElementById("output").innerHTML = response.bar;
};

var tag = document.createElement("script");
tag.src = 'somewhere_else.php?callback=foo';

document.getElementsByTagName("head")[0].appendChild(tag);
```

不使用script标签，直接使用jQuery来发起jsonp请求也是可以的：
```
$.ajax({
  url: 'http://otherdomain.com/datasource',
  dataType: 'jsonp',
  success: function(data) {
    // your code to handle data here
  }
});
```
这种方式jQuery会在请求后面加一个callback参数，便于后端返回。

jsonp需要服务器获取callback函数以后，需要配合前端返回`callback(data)`这种形式，不能直接返回data。
```
case get_str("callback", QueryString, "") of
    "" ->
        Req:ok({
            _ContentType = "application/json",
            _Headers = [],
            Response
        });
    Callback ->
        Req:ok({
            _ContentType = "text/plain",
            _Headers = [],
            [Callback, "(", Response, ")"]
        })
end;
```
jsonp只支持GET方法，且存在潜在的安全问题，我们应该尽可能使用cors。

## 跨域资源共享 cors: cross origin resource share

需要浏览器和服务器同时支持，目前所有浏览器都支持该功能，IE浏览器不能低于IE10。

浏览器发现是跨域请求就会带上Origin头，拿到服务器返回以后检查Response的 `Access-Control-Allow-Origin` 头部，
如果没有设置这个头，或者不支持当前域，表明服务器不支持CORS，则终止处理。

浏览器将请求分类两类，简单请求和非简单请求。

简单请求：
* 请求方法是以下三种之一：HEAD/GET/POST
* 头信息不超出以下字段：
  - Accept
  - Accept-Language
  - Content-Language
  - Last-Event-ID
  - Content-Type只限三个值：
    - application/x-www-form-urlencoded
    - multipart/form-data
    - text/plain

凡是不满足上面两个条件，就属于非简单请求。浏览器对两个请求的处理是不一样的。
对于简单请求，浏览器直接发出CORS请求，具体来说就是在头信息中增加一个Origin字段。
服务器根据这个值，决定是否同意这次请求。如果Origin指定的源，不在许可范围内，服务器会返回
一个正常的HTTP回应。浏览器发现这个回应的头信息没有包含Access-Control-Allow-Origin 字段，
就知道出错了。从而抛出一个错误。被XMLHttpRequest的onerror回调函数捕获。注意这种错误无法通过
状态码识别。因为HTTP回应的状态码可能是200.




简单请求头部加Origin字段，服务器如果认可则会多出几个头部信息：

* Access-Control-Allow-Origin，必须的，要么是请求的Origin字段，要么是一个`*`
* Access-Control-Allow-Credentials 布尔值，是否允许客户端发送cookie
* Access-Control-Expose-Headers CORS请求是时，XMLHttpRequest对象的getResponseHeader()
方法只能拿到6个基本字段：Cache-Control, Content-Language, Content-Type, Expire, Last-Modified, Pragma.
如果想拿到其他字段，就必须再这里指定。

CORS请求默认不发送cookie和HTTP认证信息。如果要把Cookie发到服务器，一方面要服务器同意，另一方面开发者必须在
AJAX请求中打开withCredentials属性。
```
xhr = new XMLHttpRequest();
xhr.withCredentials = true
```
如果要发送Cookie，Access-Control-Allow-Origin不能设为星号。必须指定明确的，与请求网页一致的域名。
同时，Cookie依然遵循同源政策，只有服务器域名设置的Cookie才会上传，其他域名的Cookie不会上传。

非简单请求，需要在正式通信前，增加一次HTTP查询请求，称为预检preflight。

OPTIONS


## 参考文档

* https://www.w3schools.com/js/js_json_jsonp.asp
* https://stackoverflow.com/questions/613962/is-jsonp-safe-to-use
* http://www.ruanyifeng.com/blog/2016/04/cors.html
