## select dom

> by css selector

querySelector, querySelectorAll 通过 css 选择器来选择 dom 元素，匹配不到的话，返回 null,十分强大,比较新的浏览器都支持.区别在于前者是只返回一个元素，后者如果匹配的话会返回一个 nodelist。

```javascript
// select all div tags + link tags with the class "info"
var matches = document.querySelectorAll("div, a.info");

// select all text input fields
var matches = document.querySelectorAll("input[type='text']");

var match = document.querySelector("div.foo");
// equals for the matched element
var match = document.querySelectorAll("div.foo")[0];

// select a list of matching elements, context is optional
function $(selector, context) {
  return (context || document).querySelectorAll(selector);
}

// select the first match only, context is optional
function $1(selector, context) {
  return (context || document).querySelector(selector);
}
// how to use

// select all '.bar' elements inside all '.foo' containers
var matches = $(".foo .bar");

// context example
var container = $1(".foo");
// select '.bar' elements inside this one container
var matches = $(".bar", container);
```

> by classname

getElementsByClassName 方法也是返回一个 nodelist，它的参数是要查找的类名，返回带有该类名的集合。

```javascript
var list = document.getElementsByClassName("foo");

// get the number of selected elements
console.log(list.length);

// iterate over elements and output their HTML content
for (var i = 0; i < list.length; i++) console.log(list[i].innerHTML);

//polyfill
function getElementsByClassName(className) {
  var result = [];
  var elements = document.getElementsByTagName("*");
  var pattern = new RegExp("(^|\\s)" + search + "(\\s|$)");
  for (i = 0; i < elements.length; i++) {
    if (pattern.test(elements[i].className)) {
      results.push(elements[i]);
    }
  }
  return result;
}
```

> by tagname & id

getElementsByTagName 返回的是一个 nodelist,参数是 dom 元素的标签名。

```javascript
var list = document.getElementsByTagName("a");

// get the number of selected elements
console.log(list.length);

// iterate over elements and output href attribute values
for (var i = 0; i < list.length; i++) console.log(list[i].href);
```

getElementById 返回的是一个 node,参数是 dom 元素的 id.

```javascript
var el = document.getElementById("foo");
el.innerHTML = "Hello World!";
```

> 查找一个 dom 元素的父元素

利用 dom 元素的 parentNode 属性

```javascript
var el = document.querySelector("div");
var parent = el.parentNode;
```

> 查找一个 dom 元素的兄弟节点

查找所有兄弟节点如下：

```javascript
function getSiblings(el, filter) {
  var siblings = [];
  el = el.parentNode.firstChild;
  do {
    if (!filter || filter(el)) siblings.push(el);
  } while ((el = el.nextSibling));
  return siblings;
}

// example filter function
function exampleFilter(elem) {
  return elem.nodeName.toLowerCase() == "a";
}

// usage
el = document.querySelector("div");
// get all siblings of el
var sibs = getSiblings(el);
// get only anchor element siblings of el
var sibs_a = getSiblings(el, exampleFilter);
```

单纯的查找前或者后的兄弟节点:

```javascript
var previous = el.previousSibling;
var next = el.nextSibling;

//带有filter
function getNextSiblings(el, filter) {
  var siblings = [];
  while ((el = el.nextSibling)) {
    if (!filter || filter(el)) siblings.push(el);
  }
  return siblings;
}

function getPreviousSiblings(el, filter) {
  var siblings = [];
  while ((el = el.previousSibling)) {
    if (!filter || filter(el)) siblings.push(el);
  }
  return siblings;
}
```

> 获取最近祖先节点

jq 中有 closest()方法,来满足需求，那原生呢？ 如下

```javascript
// matches polyfill
this.Element &&
  (function(ElementPrototype) {
    ElementPrototype.matches =
      ElementPrototype.matches ||
      ElementPrototype.matchesSelector ||
      ElementPrototype.webkitMatchesSelector ||
      ElementPrototype.msMatchesSelector ||
      function(selector) {
        var node = this,
          nodes = (node.parentNode || node.document).querySelectorAll(selector),
          i = -1;
        while (nodes[++i] && nodes[i] != node);
        return !!nodes[i];
      };
  })(Element.prototype);

// closest polyfill
this.Element &&
  (function(ElementPrototype) {
    ElementPrototype.closest =
      ElementPrototype.closest ||
      function(selector) {
        var el = this;
        while (el.matches && !el.matches(selector)) el = el.parentNode;
        return el.matches ? el : null;
      };
  })(Element.prototype);

var el = document.querySelector("span");
console.log(el.closest("div"));
```

> 获取元素得 children

children 和 childNodes 的区别
[https://stackoverflow.com/questions/7935689/what-is-the-difference-between-children-and-childnodes-in-javascript](https://stackoverflow.com/questions/7935689/what-is-the-difference-between-children-and-childnodes-in-javascript)

```javascript
var el = document.querySelector("div");

var children = el.childNodes,
  number_of_children = children.length;

for (var i = 0; i < number_of_children; i++) console.log(children[i].innerHTML);

获取最先最后子元素得两个便捷方法;
var firstChild = el.firstChild;
var lastChild = el.lastChild;
```
