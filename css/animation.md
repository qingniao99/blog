# css 动画

## transitions

transition 的四个 properties:

- transition-property
- transition-duration
- transition-timing-function
- transition-delay

**the order is : property duration timing-function delay.**

Not all properties can be animated, but [many of them](https://drafts.csswg.org/css-transitions/#animatable-properties). The value all means “animate all properties”.

```css
#growing {
  transition: font-size 3s, color 2s;
}

#stripe.animate {
  transform: translate(-90%);
  transition-property: transform;
  transition-duration: 9s;
  transition-timing-function: linear;
}
```

### Bezier curve

一般来说，贝塞尔曲线由四个点来控制:
1.First control point: (0,0).
2.Last control point: (1,1).
3.For intermediate points values of x must be in the interval 0..1, y can be anything.
cubic-bezier(x2, y2, x3, y3)
The x axis is the time: 0 – the starting moment, 1 – the last moment of transition-duration.
The y axis specifies the completion of the process: 0 – the starting value of the property, 1 – the final value.

[the demo wow](http://cubic-bezier.com)

step:
Timing function steps(number of steps[, start/end]) allows to split animation into steps.

```css
#stripe.animate {
  transform: translate(-90%);
  transition: transform 9s steps(9, start);
}
```

[start demo](https://javascript.info/article/css-animations/step/)

[end demo](https://javascript.info/article/css-animations/step/)

There are also shorthand values:
step-start – is the same as steps(1, start). That is, the animation starts immediately and takes 1 step. So it starts and finishes immediately, as if there were no animation.

step-end – the same as steps(1, end): make the animation in a single step at the end of transition-duration.

### Event transitionend

```javascript
// 简单实例，具体来说要考虑兼容性，而且具有多个属性运动时，某些机型会出发多个回调。

boat.onclick = function() {
  //...
  let times = 1;

  function go() {
    if (times % 2) {
      // swim to the right
      boat.classList.remove("back");
      boat.style.marginLeft = 100 * times + 200 + "px";
    } else {
      // swim to the left
      boat.classList.add("back");
      boat.style.marginLeft = 100 * times - 200 + "px";
    }
  }

  go();

  boat.addEventListener("transitionend", function() {
    times++;
    go();
  });
};
```
