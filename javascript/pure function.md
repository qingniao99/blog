[英文文章来源](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)

# 纯函数

函数是一个过程，这个过程通过一些输入，产生一些输出。术语分别叫 arguments 和 return value。

* 纯函数 pure function，一个输入，会对应一个输出，输入相同那么输出也相同。
* 不会产生边界影响(side effects)

一个纯函数来说，它是不依赖任何可变状态的，因为可变状态是不确定的或者说是不透明的。


```javascript
Math.random(); // => 0.4011148700956255
Math.random(); // => 0.8533405303023756
Math.random(); // => 0.3550692005082965

const time = () => new Date().toLocaleTimeString();
time();

// but pure function 

const highpass = (cutoff, value) => value >= cutoff;
ighpass(5, 5); // => true
highpass(5, 5); // => true
highpass(5, 5); // => true

ighpass(5, 123); // true
highpass(5, 6);   // true
highpass(5, 18);  // true
highpass(5, 1);   // false
highpass(5, 3);   // false
highpass(5, 4);   // false
```
一个纯函数来说，它是不能有边界影响的，意味着它不能改变任何外部状态。

```javascript
// impure addToCart mutates existing cart
const addToCart = (cart, item, quantity) => {
  cart.items.push({
    item,
    quantity
  });
  return cart;
};

// consider
const addToCart = (cart, item, quantity) => {
  const newCart = lodash.cloneDeep(cart);

  newCart.items.push({
    item,
    quantity
  });
  return newCart;

};
```
