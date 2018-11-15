# ajax

AJAX 是 Asynchronous JavaScript + XML 的简称 A 。AJAX 一词的实际含义为“不发生页面跳转、异步载入内容并改写页面内容的技术”。在实际操作中，AJAX 不仅仅会使用 XML 数据，很多时候也会对 JSON 或纯文本进行操作。AJAX 这个词是由 Jesse James Garrett 在 2005 年命名的。不过，在那之前就已经有使用 AJAX 的网站。

## 创建 xhr 对象:

```javascript
function getXHR() {
  if (!window.XMLHttpRequest) {
    // Internet Explorer 6
    XMLHttpRequest = function() {
      var objs = [
        "MSXML2.XMLHTTP.6.0",
        "MSXML2.XMLHTTP.3.0",
        "MSXML2.XMLHTTP",
        "Microsoft.XMLHTTP"
      ];
      for (var i = 0; i < objs.length; i++) {
        var obj = objs[i];
        try {
          return new ActiveXObject(obj);
        } catch (ignore) {}
      }
    };
  }
  return new XMLHttpRequest();
}
```

发起一个 ajax 请求，经典的五个步骤: 1.创建 xhr 对象 2.绑定异步处理 3.社情请求类型以及请求 url 4.设置请求头 5.发送请求
demo 如下

```javascript
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
  if (xhr.readyState == 4) {
    if (xhr.status == 200) {
      alert(xhr.responseText);
    }
  }
};
xhr.open("GET", "http://example.com/something");
xhr.setRequestHeader("If-Modiified-Since", "Thu 01 Jun 1970 00:00:00 GMT");
xhr.send(null);
```

onreadystatechange 这一事件处理程序将会在 XMLHttpRequest 对象的状态发生变化时被调用。用于
表示状态的 readyState 的取值范围为 0~4，其中 4 表示已经完成了对来自服务器的响应的接收处理。下表解释了 0~4 所表示的含义:

| readyState |               含义 |
| ---------- | -----------------: |
| 0          |  open() 尚未被调用 |
| 1          |  send() 尚未被调用 |
| 2          | 服务器尚未返回相应 |
| 3          |       正在接手响应 |
| 4          |   已经接收完成响应 |

在 responseText 中包含了服务器响应的字符串形式。
在这个例子中，仅传递了两个参数给 open()。但实际上它还能再接受 3 个参数。
如果传递了 false 值至第 3 个参数，XMLHttpRequest 就会执行同步通信。该参数的默认值为 true，也
就是将执行异步通信。
之后的 setRequestHeader() 用于请求头部的设置。
实际向服务器发送请求的是 send()。如果是 POST 请求类型，则会将参数所收到的数据发送至服务
器。如果是 GET 请求类型或 HEAD 请求类型等不需要发送数据的 HTTP 请求类型，则参数为 null。

## 超时:

xhr 本身并没有超时这个功能，但是可以通过 setTimeout 进行模拟.(某些高级浏览器内置了 timeout 的实现)

```javascript
var xhr = new XMLHttpRequest();
var timerId = window.setTimeout(function() {
  xhr.abort();
}, 5000); // 5 秒后将会超时
xhr.onreadystatechange = function() {
  if (request.readyState === 4) {
    // 取消超时处理
    window.clearTimeout(timeId);
  }
};

// 如果内置了timeout
xhr.timeout = 4000; // Set timeout to 4 seconds (4000 milliseconds)
xhr.ontimeout = function() {
  alert("Timed out!!!");
};
```

## 响应

responseText 现在几乎都返回的是 json,如果返回的是 html 或者 xml,我们可以使 innerHTML 和 xml.getElementByTagName 等方法来使用返回内容。
对于 json 来说，由于 JSON.parse 在某些低端浏览器不支持，我们可以使用 eval 来获取内容，但是 eval 弊端太多，所以有[json2](https://github.com/douglascrockford/JSON-js)的诞生。

## 跨域

满足任一跨域条件: 1.协议不同 2.域名不同 3.端口号不同

## 跨域的解决办法

### JSONP

动态创建 script 标签,因为 script 标签可以加载第三方域名下的东西。
根据原理，只能模拟 get 请求，不能发送 post 请求，并且这并不是存粹意义上的 ajax。

```javascript
function foo(json) {
  // 使用 json 数据进行一些操作
}
function loadData() {
  var elem = document.createElement("script");
  // 将 foo 指定为所要执行的回调函数
  // 在使用 JSONP 的 API 中，常常可以对 callback 函数的名称进行指定
  elem.src = "http://api.example.com/some-data&callback=foo";
  // 将 script 标签添加至 head 中
  // 这时 DOM 将被重建，并载入 script 标签的 src 的内容
  // 载入之后就会执行 foo 函数
  document.getElementByTagName("head")[0].append(elem);
}
```

### iframe hack

原理如下图:
![iframe](http://pgzdyozaf.bkt.clouddn.com/github/iframe.pngiframe.png)

父页面:

```javascript
function getData() {
  // 此处子 iframe 的 URL 为 other.example.com 的页面
  // 参数则是在 # 之后的数据
  frames[0].location.href =
    "http://other.example.com/api.html#" +
    "{" +
    // 这里是事实上希望执行的 API
    '"api": "http://other.example.com/some-data",' +
    // 在子 iframe 中指定的孙 iframe 的 URL
    '"callback": "http://my.example.com/callback.html"' +
    "}";
}
// 在跨源通信中作为回调函数被执行的函数
// 由孙 iframe 调用
function callback(param) {
  document.getElementById("result").innerHTML = param;
  frames[0].frames[0].location.href = "dummy.gif";
}
```

子页面:

```javascript
function executeApi() {
  // 将 location.hash 的第一个字符 (#) 去除，将剩余部分以 JSON 格式进行分析
  var param = JSON.parse(location.hash.substring(1));
  var xhr = new XHMHttpRequest();
  xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
      var iframe = document.getElementById("grandchild-iframe");
      iframe.location.href = param.callback + "#" + xhr.responseText;
    }
  };
  xhr.open(param.api, "GET");
  xhr.send(null);
}
```

孙页面:

```javascript
window.onload = function() {
  window.top.callback(location.hash);
};
```

### CORS

在响应中如果包含 Access-Control-Allow-Origin 这一 HTTP 头部，以指定可以访问的源。如果 Access-Control-Allow-Origin 这一头部的值被指定为了 "\*" 的话，则表示允许来自任意源
的访问。
在通过 XMLHttpRequest 进行跨源通信时，默认为不发送 Cookie。如果要发送 Cookie，则必须将
withCredentials 属性设置为 true。

此外还有 HTML5 的 postMessage,后台请求代理等措施可以跨域。

---

社区有很多很成熟的 ajax 封装，像经典的 jq.ajax,还有特别火的[axios](https://github.com/axios/axios)等。
