# repain 和 reflow

## repain

当元素可见性发生了变化的时候，就会产生 repain. 比如 opacity，background-color,visibility,outline 等发生了变化。浏览器的 repain 操作开销很大，因为当一个元素的可见性发生了变化，浏览器必须要 check 与之相关的其他节点，在它下面的节点的可见性很可能会受到影响。

## reflow

reflow 的影响更大，重新计算元素的大小和位置，会导致局部的或者全局的 re-render。

当 repain 或者 reflow 发生的时候，浏览器的性能会极大的降低。
![reflow time](https://developers.google.com/speed/images/reflow-chart.png)

## 哪些骚操作会引起 reflow 和 repain 呢？

##### Box metrics

- `elem.offsetLeft`, `elem.offsetTop`, `elem.offsetWidth`, `elem.offsetHeight`, `elem.offsetParent`
- `elem.clientLeft`, `elem.clientTop`, `elem.clientWidth`, `elem.clientHeight`
- `elem.getClientRects()`, `elem.getBoundingClientRect()`

##### Scroll stuff

- `elem.scrollBy()`, `elem.scrollTo()`
- `elem.scrollIntoView()`, `elem.scrollIntoViewIfNeeded()`
- `elem.scrollWidth`, `elem.scrollHeight`
- `elem.scrollLeft`, `elem.scrollTop` also, setting them

##### Focus

- `elem.focus()` can trigger a _double_ forced layout ([source](https://cs.chromium.org/chromium/src/third_party/WebKit/Source/core/dom/Element.cpp?q=updateLayoutIgnorePendingStylesheets+-f:out+-f:test&sq=package:chromium&dr=C)&l=2923)

##### Also…

- `elem.computedRole`, `elem.computedName`
- `elem.innerText` ([source](https://cs.chromium.org/chromium/src/third_party/WebKit/Source/core/dom/Element.cpp?q=updateLayoutIgnorePendingStylesheets+-f:out+-f:test&sq=package:chromium&dr=C)&l=3440))

### getComputedStyle

`window.getComputedStyle()` will typically force style recalc

`window.getComputedStyle()` will force layout, as well, if any of the following is true:

1. The element is in a shadow tree
1. There are media queries (viewport-related ones). Specifically, one of the following: ([source](https://cs.chromium.org/chromium/src/third_party/WebKit/Source/core/css/MediaQueryExp.cpp?type=cs&q=f:MediaQueryExp.cpp+MediaQueryExp::IsViewportDependent&l=192))

- `min-width`, `min-height`, `max-width`, `max-height`, `width`, `height`
- `aspect-ratio`, `min-aspect-ratio`, `max-aspect-ratio`
- `device-pixel-ratio`, `resolution`, `orientation` , `min-device-pixel-ratio`, `max-device-pixel-ratio`

1. The property requested is one of the following: ([source](https://cs.chromium.org/chromium/src/third_party/WebKit/Source/core/css/CSSComputedStyleDeclaration.cpp?dr=C&q=f:CSSComputedStyleDeclaration.cpp+isLayoutDependent&sq=package:chromium))

- `height`, `width`
- `top`, `right`, `bottom`, `left`
- `margin` [`-top`, `-right`, `-bottom`, `-left`, or *shorthand*] only if the margin is fixed.
- `padding` [`-top`, `-right`, `-bottom`, `-left`, or *shorthand*] only if the padding is fixed.
- `transform`, `transform-origin`, `perspective-origin`
- `translate`, `rotate`, `scale`
- `grid`, `grid-template`, `grid-template-columns`, `grid-template-rows`
- `perspective-origin`
- These items were previously in the list but appear to not be any longer (as of Feb 2018): `motion-path`, `motion-offset`, `motion-rotation`, `x`, `y`, `rx`, `ry`

### window

- `window.scrollX`, `window.scrollY`
- `window.innerHeight`, `window.innerWidth`
- `window.getMatchedCSSRules()` only forces style

### Forms

- `inputElem.focus()`
- `inputElem.select()`, `textareaElem.select()`

### Mouse events

- `mouseEvt.layerX`, `mouseEvt.layerY`, `mouseEvt.offsetX`, `mouseEvt.offsetY` ([source](https://cs.chromium.org/chromium/src/third_party/WebKit/Source/core/events/MouseEvent.cpp?type=cs&q=f:Mouse+f:cpp+::computeRelativePosition&sq=package:chromium&l=517))

### document

- `doc.scrollingElement` only forces style

### Range

- `range.getClientRects()`, `range.getBoundingClientRect()`

### SVG

- Quite a lot; haven't made an exhaustive list , but [Tony Gentilcore's 2011 Layout Triggering List](http://gent.ilcore.com/2011/03/how-not-to-trigger-layout-in-webkit.html) pointed to a few.

### contenteditable

- Lots & lots of stuff, …including copying an image to clipboard ([source](https://cs.chromium.org/search/?q=UpdateStyleAndLayoutIgnorePendingStylesheets+file:%5Esrc/third_party/WebKit/Source/core/editing/+package:%5Echromium$&type=cs))

> 简单优化

减少不必要的 DOM 深度。
在 DOM 树中的一个级别进行更改可能会致使该树的所有级别（上至根节点，下至所修改节点的子级）都随之变化。这会导致花费更多的时间来执行重排。

尽可能减少 CSS 规则的数量，并移除未使用的 CSS 规则。

如果您想进行复杂的渲染更改（例如动画），请在流程外执行此操作。您可以使用 position-absolute 或 position-fixed 来实现此目的。

避免使用不必要且复杂的 CSS 选择器（尤其是后代选择器），因为此类选择器需要耗用更多的 CPU 处理能力来执行选择器匹配。

> some blog

[chrome more reflow](https://developers.google.com/speed/docs/insights/browser-reflow)

[who fouces layout](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)

[10ways to improve](https://www.sitepoint.com/10-ways-minimize-reflows-improve-performance/)
