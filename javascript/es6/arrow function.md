# 箭头函数

## 核心结构：

```javascript
(argument1, argument2, ...argumentN) => {
  // function body
};
```

在括号里面有一系列的参数，接着跟着一个箭头符号=>，最后是函数体。这跟传统的函数很相像，只是我们省略了 function 关键字，并且添加了一个=>在参数后面。
并且，这里也有很多种情况，让箭头函数结构变得更加的简洁。
首先，如果函数体里面是一个单独的表达式，你可以省略大括号直接将表达式写在一行，并且表达式的结果也将会被函数直接返回。比如：

const add = (a, b) => a + b;

其次，如果这传入的是一个单独的参数，你也可省略参数部分的括号。比如：

const getFirst = array => array[0];

## 封闭的上下文作用域

**不像其他形式的函数，箭头函数并没有他们自己的执行上下文。实际上，这就意味着代码中的 this 和 arguments 都是继承自他们的父函数。**

```javascript
const test = {
  name: "test object",

  createAnonFunction: function() {
    return function() {
      console.log(this.name);

      console.log(arguments);
    };
  },

  createArrowFunction: function() {
    return () => {
      console.log(this.name);

      console.log(arguments);
    };
  }
};

const anon = test.createAnonFunction("hello", "world");
//返回匿名函数
const arrow = test.createArrowFunction("hello", "world");
anon();
//undefined
//{}
// this->window
arrow();
//test object
//object { '0': 'hello', '1': 'world' }
//this->test

```

第一个匿名函数有自己的上下文（指向并非test对象），当你调用的时候没有参考的this.name的属性，（注意：现在this指向window），也没有创建它时调用的参数。另一个，箭头函数与创建它的函数有相同的上下文，让其可以访问参数arguments和对象。

