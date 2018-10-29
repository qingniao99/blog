# 缓存

浏览器加载资源，对加载过的资源根据 http 报文的头部信息决定是否进行缓存处理。

> Cache-Control

no-cache 再次请求时，不能使用本次文件，需要和服务端通信一次，确定是否使用缓存文件
no-store 禁止缓存
private 仅 UA 可缓存
public 大家都可以缓存
当 cache-control 可缓存时，max-age:1000 或者 expires 等控制缓存时间的字段就生效了

> Etag

资源本身经常发生变动，cache-control 有些时候就会有缓存弊端，看不到最新页面。etag 响应头字段表示资源的版本，浏览器在发送请求时会带 If-None-Match 头字段， 来询问服务器该版本是否仍然可用。如果服务器发现该版本仍然是最新的， 就可以返回 304 状态码指示 UA 继续使用缓存

> Last-Modified

与 Etag 类似，Last-Modified HTTP 响应头也用来标识资源的有效性。 不同的是使用修改时间而不是实体标签。对应的请求头字段为 If-Modified-Since

![best do](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/images/http-cache-decision-tree.png?hl=zh-cn)

> some blog

[working with http cache](https://www.brianstorti.com/working-with-http-cache/)
[HTTP 缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn#cache-control)
[使用 HTTP 缓存](https://harttle.land/2017/04/04/using-http-cache.html)
