---
title: CSS知识点梳理(17)-过渡（Transition）
categories: 技术总结
date: 2020-01-06 10:46:11
tags: css
---

通常，当一个 CSS 属性值改变时，改变是瞬间发生的。新的属性在重绘、或必要时回流+重绘受影响内容所需要的毫秒内（似乎时瞬间，渲染时间小于 16 毫秒）替换旧属性。CSS 中`transition`提供了一种方法来控制属性在一段时间内的改变，可以使属性值逐渐 变化。

### 过渡属性

在 CSS 中，`transition`有四个属性：`transition-property`,`transition-duration`,`transition-timing-function`,`transition-delay`.  
元素上的`transition`与常规样式一起声明。每当目标属性更改时，如果在该属性上设置了`transition`，则浏览器使用`transition`使更改逐步进行。

#### transition-property

- `transition-property`指定要过渡的 CSS 属性名称。默认值`all`，即过渡所有可动画的属性。
- `all`在`transition-property`值列表的第一项，其后再跟需要特殊过渡的属性列表。可有大部分属性相同过渡效果+少数属性特殊过渡效果的组合。

  ```CSS
  div {
      color: #f00;
      border: 1px solid #00ff00;
      border-radius: 0;
      transform: scale(1) rotate(0deg);
      opacity: 1;
      box-shadow: 3px 3px rgba(0, 0, 0, 0.1);
      width: 50px;
      padding: 100px;
      transition-property: all, border-radius, opacity;
      transition-duration: 1s, 2s, 3s;
  }
  ```

  `border-radius`过渡持续时间 2s,`opacity`过渡持续时间 3s,其他 CSS 属性过渡持续时间 1s.

- `transitionend`事件，在每次**过渡结束时**触发。无论属性是单独声明，还是作为`all`声明的一部分，都会触发`transitionend`.**简写属性中的每一个过渡属性都有自己的`transitionend`事件。例如`border`简写属性，会产生 8 个`transitionend`事件(`border-width`（4 个），`border-color`（4 个）,`border-style`不是可动画的属性，没有该事件)：**
  - border-left-width
  - border-right-width
  - border-top-width
  - border-bottom-width
  - border-top-color
  - border-left-color
  - border-right-color
  - border-bottom-color
- `transitionend`事件，包含 3 个特殊的事件属性
  - `propertyName`,发生过渡的 CSS 属性名称
  - `pseudoElement`,发生过渡的伪元素（2 个分号）
  - `elapsedTime`,运行过渡所需的时间，以秒为单位；`transition-duration`属性定义的时间。

#### transition-duration

- `transiton-duration`必须数正值，且带单位`s,ms`，默认值`0s`。负值无效，则整个属性值都无效。
- `transiton-duration`使用逗号分隔的时间值同`transition-property`属性列出的 CSS 属性值声明相对应。

  - 如果`transition-duration`数量大于`transiton-property`,则忽略多余`transition-duration`的值
  - 如果`transition-duration`数量小于`transiton-property`,则重复`transition-duration`的值

  ```CSS
  div {
      ...
      transition-property: color, border, border-radius, transform, opacity, box-shadow, width, padding;
      transiion-duration: 100ms, 200ms;
  }
  ```

  基数属性的过渡持续时间是 100ms,偶数属性的过渡持续时间是 200ms。

#### transition-timing-function

`transiton-timing-function`可以控制过渡变化的速度。一种是平滑的曲线折线（`cubic-bezier()`），一种是跳跃的折线形式（`steps(n, start)`）

- cubic-bezier() [贝塞尔曲线绘制工具](https://cubic-bezier.com/)

  | Timeing function |         Description          |      Cubic Bezier value       |
  | :--------------: | :--------------------------: | :---------------------------: |
  |  cubic-bezier()  |    指定一个三次贝塞尔曲线    |  cubic-bezier (x1,y1,x2,y2)   |
  |       ease       | 开始缓慢，然后加速，然后缓慢 | cubic-bezier(0.25,0.1,0.25,1) |
  |      linear      |   整个过程以相同的速度运行   |     cubic-bezier(0,0,1,1)     |
  |     ease-in      |      开始缓慢，然后加速      |   cubic-bezier(0.42,0,1,1)    |
  |     ease-out     |     开始很快，然后慢下来     |   cubic-bezier(0,0,0.58,1)    |
  |   ease-in-out    | 开始和结束快一些，中间慢一些 |  cubic-bezier(0.42,0,0.58,1)  |

  ![Curve representations of named cubic Bezier functions](/image/cubic_bezier.jpg)

- Steps()
  `steps(n, start/end)`,参数一，把过渡分成几段，即在时间上分成几段去显示执行。参数二，表示分成几段后，在`start`还是`end`去执行动画，默认值`end`.

  |         Timing funcion         |                        Definition                         |
  | :----------------------------: | :-------------------------------------------------------: |
  |           step-start           | 在过渡过程中始终停留在最后 的关键帧，等于 steps(1, start) |
  |            step-end            |  在过渡过程中始终停留在最初的关键帧， 等于 steps(1, end)  |
  | steps(n, start) ,steps(n, end) | 过渡分成 n 个阶段，是在每个阶段的 start 或 end 去执行动画 |

#### transition-delay

- `transition-delay`默认值为 0s。可以为负值。
- 同`transition-duration`和`transition-timing-function`一样，当逗号分隔的`transition-delay`值的数量超过 逗号分隔的`transition-property`值的数量时，额外的`transition-delay`值被忽略 。当`transition-delay`数量少于`transition-property`的值时，`transition-delay`值会重复。
- 当`transition-delay`的负值的绝对值小于`transition-duration`时，则会立即开始过渡；如果大于，CSS 属性值改变时立即的，就如同没有应用`transition`，也不会有`transitionend`事件触发。

  ```CSS
  div {
      transform: translateX(0);
      transition-property: transform;
      transition-duration: 200ms;
      transition-timing-function: linear;
      transition-delay: -150ms;
  }
  div:hover {
      transform: translateX(200px);
  }

  ```

  - 200ms 过渡，延时为-150ms,则过渡效果将在过渡过程的 3/4 处开始，并持续 50ms.即鼠标悬停时立即沿 x 轴移动到 150px，然后再 50ms 内从 150px 过渡到 200px。
  - 当从悬停 状态回到原始状态时，**默认情况下，会应用相同的`transition-delay`**。即当鼠标移走时，它将跳至沿 x 轴 50px 位置，然后花费 50ms 返回到 x 轴的 0px 的初始位置。

#### transiton 简写

- `transition`简写表示`transiton:<transition-property> <transition-duration> <transiton-timing-function> <transition-delay>`,默认值:`transiton: all 0s ease 0s`.
- 如果省略`transition-property`，则其值为`all`。如果只有一个`<time>`值 ，它代表`transition-duration`，那个`transition-delay`被省略，默认为 0s.

### 反向:过渡

- 由于过渡声明仅处于全局状态，所以鼠标悬停和鼠标离开都会使用相同的`transition`声明。我们可以在多个状态中覆写`transition`属性。
- 过渡分为 2 个阶段：`forward`前进，`reverse`反向阶段。若前进阶段进行一段时间后进入反向阶段，则反向阶段的初始值是前进阶段结束的瞬时值。

#### 反向过渡中断了正在进行的过渡

- **当过渡在完成之前被中断，属性值将重置为开始过渡之前的值，并且属性将过渡回那些值**CSS 规范提供了式恢复过渡更短的方案

### 可动画属性和值

某些 CSS 属性是可以使用`transition`和`animation`来是他们的值在给定的时间内逐渐改变。随着 CSS 的发展，[可动画属性的列表](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties)也有新的补充。

- 判断一个属性是否是可动画属性的关键，它**计算值（computed value）**是否可以内插一个值。`Interpolation 插值`是在已知数据点的值之间构造新的数据点。

### 降级：过渡是一种 增强

由于过渡通常是渐进的增强，所以没有针对老式浏览器的`polyfill`。如果浏览器不支持过渡，当样式发生改变时，则立即从初始状态过渡到最终状态罢了。
