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

## keyframe

[summary from aaronjs blog](http://www.cnblogs.com/aaronjs/p/4642015.html)

Animation的几个属性:

- animation-name
- animation-duration
- animation-delay
- animation-iteration-count
- animation-direction
- animation-play-state
- animation-fill-mode
- animation-timing-function

```css
.test1 {
    width: 90px;
    height: 60px;
    -webkit-animation-name: skyset;
    -webkit-animation-duration: 2000ms;
    -webkit-animation-iteration-count: infinite; /*无限循环*/
    -webkit-animation-timing-function: linear;
}
@-webkit-keyframes skyset {
    0% { background: red;}
    50%{ background: blue}
    100% {background: yellow;}
}
```
timing-function:linear 定义的是一个匀速变化的动画，就是在2秒内，从红色过度到蓝色到黄色，是一个很线性的颜色变化
如果要实现帧动画效果而不是线性的变化就需要引入step这个值了，换句话就是没有过渡的效果，而是一帧帧的变化.

理解steps
steps 函数指定了一个阶跃函数
第一个参数指定了时间函数中的间隔数量（必须是正整数）
第二个参数可选，接受 start 和 end 两个值，指定在每个间隔的起点或是终点发生阶跃变化，默认为 end。
step-start等同于steps(1,start)，动画分成1步，动画执行时为开始左侧端点的部分为开始；
step-end等同于steps(1,end)：动画分成一步，动画执行时以结尾端点为开始，默认值为end。

[timing-function](https://www.w3.org/TR/css-transitions-1/#transition-timing-function-property)

**timing-function 作用于每两个关键帧之间，而不是整个动画**

![step iimage](https://images0.cnblogs.com/i/596159/201406/091121212334792.png)

[how to use steps](https://designmodo.com/steps-css-animations/)

