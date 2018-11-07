## operate dom

> 创建节点

主要用的两个，createElement，createTextNode

```javascript
var el = document.createElement("div");
el.innerHTML = "<p>Hello World!</p>";

var textnode = document.createTextNode("Hello World!");
el.appendChild(textnode);
```

> 在某个元素前或者后插入节点

insertAfter 和 insertBefore

```javascripit
function insertAfter(el, referenceNode) {
    referenceNode.parentNode.insertBefore(el, referenceNode.nextSibling);
}

// example
var newEl = document.createElement('div');
newEl.innerHTML = '<p>Hello World!</p>';

var ref = document.querySelector('div.before');

insertAfter(newEl, ref);




function insertBefore(el, referenceNode) {
    referenceNode.parentNode.insertBefore(el, referenceNode);
}

// example
var newEl = document.createElement('div');
newEl.innerHTML = '<p>Hello World!</p>';

var ref = document.querySelector('div.before');

insertBefore(newEl, ref);
```

> Append or prepend to an element

```javascript
var el = document.querySelector("div");

// get element content as string
console.log(el.innerHTML);

// append to the element's content
el.innerHTML += "<p>Hello World!</p>";

// prepend to the element's content
el.innerHTML = "<p>Hello World!</p>" + el.innerHTML;

var el = document.querySelector("div");

// create a p element for inserting in el
var newEl = document.createElement("p");
// use the innerHTML property for inserting HTML content
// or append a textNode to the p element
newEl.appendChild(document.createTextNode("Hello World!"));

// append p as a new child to el
el.appendChild(newEl);

// same result with insertBefore()
el.insertBefore(newEl, null);

// use as second argument the child node you want to insert the new node before
// example: prepend newEl as first child to el
el.insertBefore(newEl, el.childNodes[0] || null);
```

> 替换节点

replaceChild 方法

```javascript
// select the element that will be replaced
var el = document.querySelector("div");

// <a href="/javascript/manipulation/creating-a-dom-element-51/">create a new element</a> that will take the place of "el"
var newEl = document.createElement("p");
newEl.innerHTML = "<b>Hello World!</b>";

// replace el with newEL
el.parentNode.replaceChild(newEl, el);
```

> 移除节点

```javascripit
var el = document.querySelector('div'); // select the first returned <div> element
el.parentNode.removeChild(el);
```

> clone 节点

```javascript
var el = document.querySelector("div");

//cloneNode(true) creates a deep copy of the selected DOM element including attributes and child elements. To clone only the node and its attributes, pass false as argument to cloneNode().
var foo = el.cloneNode(true);
```
