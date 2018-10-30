# 浏览器存储

## 只讨论 localStorage 和 sessionStorage

localStorage:持久化存储，有 4M 甚至更多的空间,存储内容是字符串。
sessionStorage:会话式存储，当会话关闭时，存储失效，存储内容是字符串。

注意：特定情况下，浏览器隐私模式，window.localStorage 或者 window.sessionStorage 返回 true ,但是在使用时会报错！
mozilla 官方给了一个比较全的判断函数，如下

```javascript
function storageAvailable(type) {
  try {
    var storage = window[type],
      x = "__storage_test__";
    storage.setItem(x, x);
    storage.removeItem(x);
    return true;
  } catch (e) {
    return (
      e instanceof DOMException &&
      // everything except Firefox
      (e.code === 22 ||
        // Firefox
        e.code === 1014 ||
        // test name field too, because code might not be present
        // everything except Firefox
        e.name === "QuotaExceededError" ||
        // Firefox
        e.name === "NS_ERROR_DOM_QUOTA_REACHED") &&
      // acknowledge QuotaExceededError only if there's something already stored
      storage.length !== 0
    );
  }
}

if (storageAvailable("localStorage")) {
  // Yippee! We can use localStorage awesomeness
} else {
  // Too bad, no localStorage for us
}
```

github 上也有个封装好的库，看起来不错，[看这里](https://github.com/marcuswestin/store.js)

> some blog

[Web_Storage](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)

[web strorage](https://developers.google.com/web/fundamentals/instant-and-offline/web-storage/)

[client-side-storage](https://www.html5rocks.com/en/tutorials/offline/storage/#toc-introduction)
