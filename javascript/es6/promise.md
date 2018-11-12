# promise

在浏览器中，JavaScript 与因浏览器而异的其他 N 种任务共享一个线程，但是通常情况下 JavaScript 与绘制、更新样式和处理用户操作（例如，高亮显示文本以及与格式控件交互）处于同一队列。操作其中一项任务会延迟其他任务。

为了提高体验，事件和回调的用法在 js 里面已经司空见惯。

```javascript
var img1 = document.querySelector(".img-1");

img1.addEventListener("load", function() {
  // woo yey image loaded
});

img1.addEventListener("error", function() {
  // argh everything's broken
});
```

promise 有点类似于事件侦听器，但有以下两点区别:

- promise 只能成功或失败一次，而不能成功或失败两次，也不能从成功转为失败或从失败转为成功。
- 如果 promise 已成功或失败，且您之后添加了成功/失败回调，则将会调用正确的回调，即使事件发生在先。

这对于异步成功/失败尤为有用，因为您可能对某些功能可用的准确时间不是那么关注，更多地是关注对结果作出的反应。
promise 可以是：

- 已执行 - 与 promise 有关的操作成功
- 已拒绝 - 与 promise 有关的操作失败
- 待定 - 尚未执行或拒绝

```javascript
var promise = new Promise(function(resolve, reject) {

  if (/* everything turned out fine */) {
    resolve("Stuff worked!")
  }
  else {

reject(Error("It broke"));
  }
});
```

Promise 构造函数包含一个参数和一个带有 resolve（解析）和 reject（拒绝）两个参数的回调。在回调中执行一些操作（例如异步），如果一切都正常，则调用 resolve，否则调用 reject。

与普通旧版 JavaScript 中的 throw 一样，通常拒绝时会给出 Error 对象，但这不是必须的。Error 对象的优点在于它们能够捕捉堆叠追踪，因而使得调试工具非常有用。
then() 包含两个参数：一个用于成功情形的回调和一个用于失败情形的回调。这两个都是可选的，因此您可以只添加一个用于成功情形或失败情形的回调。

```javascript
promise.then(
  function(result) {
    console.log(result); // "Stuff worked!"
  },
  function(err) {
    console.log(err); // Error: "It broke"
  }
);
```

[es6-promiese-polyfill](https://github.com/stefanpenner/es6-promise)

> 对 XMLHttpRequest 执行 promise

```javascript
function get(url) {
  // Return a new promise.
  return new Promise(function(resolve, reject) {
    // Do the usual XHR stuff
    var req = new XMLHttpRequest();
    req.open("GET", url);

    req.onload = function() {
      // This is called even on 404 etc
      // so check the status
      if (req.status == 200) {
        // Resolve the promise with the response text
        resolve(req.response);
      } else {
        // Otherwise reject with the status text
        // which will hopefully be a meaningful error
        reject(Error(req.statusText));
      }
    };

    // Handle network errors
    req.onerror = function() {
      reject(Error("Network Error"));
    };

    // Make the request
    req.send();
  });
}

get("story.json").then(
  function(response) {
    console.log("Success!", response);
  },
  function(error) {
    console.error("Failed!", error);
  }
);
```

> 链接

then() 不是最终部分，可以将各个 then 链接在一起来改变值，或依次运行额外的异步操作。
当您从 then() 回调中返回某些内容时，这有点儿神奇。如果返回一个值，则会以该值调用下一个 then()。但是，如果您返回类似于 promise 的内容，下一个 then() 则会等待，并仅在 promise 产生结果（成功/失败）时调用.

```javascript
getJSON("story.json")
  .then(function(story) {
    return getJSON(story.chapterUrls[0]);
  })
  .then(function(chapter1) {
    console.log("Got chapter 1!", chapter1);
  });
```

> 异常捕捉

```javascript
get("story.json").then(
  function(response) {
    console.log("Success!", response);
  },
  function(error) {
    console.log("Failed!", error);
  }
);

get("story.json")
  .then(function(response) {
    console.log("Success!", response);
  })
  .catch(function(error) {
    console.log("Failed!", error);
  });
```

catch() 没有任何特殊之处，它只是 then(undefined, func) 的锦上添花，但可读性更强。注意，以上两个代码示例行为并不相同，后者相当于：

```javascript
get("story.json")
  .then(function(response) {
    console.log("Success!", response);
  })
  .then(undefined, function(error) {
    console.log("Failed!", error);
  });
```

```javascript
asyncThing1()
  .then(function() {
    return asyncThing2();
  })
  .then(function() {
    return asyncThing3();
  })
  .catch(function(err) {
    return asyncRecovery1();
  })
  .then(
    function() {
      return asyncThing4();
    },
    function(err) {
      return asyncRecovery2();
    }
  )
  .catch(function(err) {
    console.log("Don't worry about it");
  })
  .then(function() {
    console.log("All done!");
  });
```

![上述过程](https://developers.google.com/web/fundamentals/primers/imgs/promise-flow.svg?hl=zh-cn)


> 并行式和顺序式:两者兼得

```javascript
getJSON('story.json').then(function(story) {
  addHtmlToPage(story.heading);

  // TODO: for each url in story.chapterUrls, fetch &amp; display
}).then(function() {
  // And we're all done!
  addTextToPage("All done");
}).catch(function(err) {
  // Catch any error that happened along the way
  addTextToPage("Argh, broken: " + err.message);
}).then(function() {
  // Always hide the spinner
  document.querySelector('.spinner').style.display = 'none';
})
```
但是我们如何遍历 URL 并按顺序获取呢？以下方法行不通：
```javascript
story.chapterUrls.forEach(function(chapterUrl) {
  // Fetch chapter
  getJSON(chapterUrl).then(function(chapter) {
    // and add it to the page
    addHtmlToPage(chapter.html);
  });
})
```
forEach 不是异步的，内容将按照下载的顺序显示，这就乱套了.
我们想要将 chapterUrls 数组转变为 promise 序列，这可通过 then() 来实现：
```javascript
// Start off with a promise that always resolves
var sequence = Promise.resolve();

// Loop through our chapter urls
story.chapterUrls.forEach(function(chapterUrl) {
  // Add these actions to the end of the sequence
  sequence = sequence.then(function() {
    return getJSON(chapterUrl);
  }).then(function(chapter) {
    addHtmlToPage(chapter.html);
  });
})
```
Promise.resolve()，这种 promise 可解析为您赋予的任何值。如果向其传递一个 Promise 实例，它也会将其返回（Note: 这是对本规范的一处更改，某些实现尚未遵循）。如果将类似于 promise 的内容（带有 then() 方法）传递给它，它将创建以相同方式执行/拒绝的真正 Promise。如果向其传递任何其他值，例如 Promise.resolve('Hello')，它在执行时将以该值创建一个 promise。如果调用时不带任何值（如上所示），它在执行时将返回“undefined”。

此外还有 Promise.reject(val)，它创建的 promise 在拒绝时将返回赋予的值（或“undefined”）。

我们可以使用 array.reduce 将上述代码整理如下：
```javascript
// Loop through our chapter urls
story.chapterUrls.reduce(function(sequence, chapterUrl) {
  // Add these actions to the end of the sequence
  return sequence.then(function() {
    return getJSON(chapterUrl);
  }).then(function(chapter) {
    addHtmlToPage(chapter.html);
  });
}, Promise.resolve())
```
这与之前示例的做法相同，但是不需要独立的“sequence”变量。我们的 reduce 回调针对数组中的每项内容进行调用。首次调用时，“sequence”为 Promise.resolve()，但是对于余下的调用，“sequence”为我们从之前调用中返回的值。array.reduce 确实非常有用，它将数组浓缩为一个简单的值（在本例中，该值为 promise）。
汇总一下:
```javascript
getJSON('story.json').then(function(story) {
  addHtmlToPage(story.heading);

  return story.chapterUrls.reduce(function(sequence, chapterUrl) {
    // Once the last chapter's promise is done…
    return sequence.then(function() {
      // …fetch the next chapter
      return getJSON(chapterUrl);
    }).then(function(chapter) {
      // and add it to the page
      addHtmlToPage(chapter.html);
    });
  }, Promise.resolve());
}).then(function() {
  // And we're all done!
  addTextToPage("All done");
}).catch(function(err) {
  // Catch any error that happened along the way
  addTextToPage("Argh, broken: " + err.message);
}).then(function() {
  // Always hide the spinner
  document.querySelector('.spinner').style.display = 'none';
})
```


> some blog

[chrome promise](https://developers.google.com/web/fundamentals/primers/promises?hl=zh-cn)

[basics](https://javascript.info/promise-basics)