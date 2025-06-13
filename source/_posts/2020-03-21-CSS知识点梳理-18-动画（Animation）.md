---
title: CSS知识点梳理(18)-动画（Animation）
categories: 技术总结
date: 2020-03-21 16:41:40
tags: css
---

使用 CSS 过渡，属性值的开始和结束状态由现有的属性值控制，且几乎无法控制事物随时间的变化。CSS 动画类似于 CSS 过渡，其 CSS 属性的值也会随时间变化，但对于这些变化如何发生提供了更多的控制。具体地说，CSS 关键帧（`keyframe`）动画让我们决定一个动画是否和如何重复，并给我们颗粒级的控制什么发生在整个动画，甚至更多。CSS 过渡触发隐式属性值改变，动画则在应用`keyframe`属性时显性执行。

### 定义关键帧

要使一个元素产生动画效果，我们需要设置关键帧动画的名称。为此，我们需要一个命名的关键帧动画.第一步，使用`@keyframes 规则`定义可重用的 CSS 关键帧动画命名。然后，将动画附加 到元素或伪元素上。  
一个`@keyframes 规则`包含动画标识符或名称，以及一个或多个关键帧块(`keyframe blocks`)。每一个关键帧块包含一个或多个关键帧选择器，并带有属性-值对的声明块。整个`@keyframes 规则`指定了动画的单次完整的重复行为。动画可重复 0 次或多次，这取决于`animation-iteration-count`属性值。  
关键帧选择器（`keyframe selectors`）是整个动画持续时间的时间百分比位置。它们声明为百分比，或者使用关键字`from`和`to`

```CSS
/* 一般结构 */
@keyframes animation_identifier {
    keyframe_selector {
        property: value;
    }
    keyframe_selector {
        property: value;
    }
}

/* 实例 */
@keyframes fadeout {
    from {
       opacity: 1;
    }
    to {
        opacity: 0;
    }
}

@keyframes color-pop {
    0% {
        color: black;
        background-color: white;
    }
    33%, 75% {
        color: gray;
        background-color: yellow;
    }
    100% {
        color: white;
        background-color: orange;
    }
}
```

### 可动画属性

并非所有的属性都是可动画化的。在动画关键帧中，如果给出一个不能被动画化的属性，它就会并忽略。  
只要在至少一个块中包含一个动画属性，并且该属性的值与非动画属性值不同，**且在这两个值之间有一个可计算的中点**，那么该属性就会被动画化。

### 动画元素

一旦创建了关键帧动画，就可以将该动画应用于元素或伪元素。CSS 提供了许多动画属性来将关键帧动画附加到元素并控制其进度。至少，你需要包含动画名称和动画的持续时间（如果希望动画实际上是可见的，否则，动画将在 0 秒时间内发生）  
有 2 种方法可以将动画属性附加到元素上：1.分别的包含所有的动画属性；2.使用`animation`的速记属性在一行内声明所有的动画属性。

#### `animation-name`

- `animation-name`属性以逗号分隔的 关键帧动画名称列表作为其值。
- 如果其中一个关键帧名称不存在，该动画系列不会失败。仅仅是失败的动画被忽略，有效的动画被应用。
- 如果对一个元素应用多个动画，并且这些动画具有重复的属性，则后面的动画将覆盖前面动画中的属性值。

#### `animation-duration`

- `animation-duration`属性定义了一个动画循环需要多长时间，以秒或毫秒为单位。
- 如果不声明`animation-duration`,动画任然会以 0s 持续时间运行。并且`anmationstart`和`animationend`事件会被触发。
- 此属性不允许使用负的时间值。
- 如果有一个以上的动画，则使用逗号分隔
  ```CSS
  div {
      animation-name: change_bgcolor, round, W;
      animation-duration: 200ms, 100ms, 0.5s;
  }
  ```
- 在指定持续时间时，必须包含`秒(s)`或`毫秒(ms)`单位。
- 如果在以逗号分隔的时间列表中提供了无效的值，如`animation-duration: 200ms, 0, 0.5s`，则整个声明将失败。
- 在逗号分隔的属性值列表中，使用比`animation-name`值更少的`animation-duration`值，不会失败，相反，这些值将作为一个组进行重复
  ```CSS
  animation-name: a, b, c, d, e;
  animation-duration:200ms, 5s;
  /*相当于 animation-duration: 200ms, 5s, 200ms, 5s, 200ms; */
  ```
- 如果`animation-duration`的值的数量超出，超出的值会被忽略。
- 如果`animation-name`其中的一个值不存在，则该值会被忽略，那么`animation-duration`相对应的值也会被忽略，但整个动画系列和动画持续时间依然有效。

#### `animation-iteration-count`

- 默认值是`1`
- 如果`animation-iteration-count`的值不是整数，动画任然运行，但是会在重复时等比例的位置中断。例如，如果一个 8 秒的动画，其`animation-iteration-count`值为`0.25`，则动画将播放`2s(0.25 * 8)`的时间，在 2s 后结束。
- 负数是不允许的，如果指定的值无效，则会取默认值:`1`.
- `0`值是有效值，动画任然运行，但次数为 0，类似于`animation-duration: 0s`。会触发`animationstart`和`animationend`事件。
- 使用逗号分隔的列表值，额外的值将被忽略，缺少的值会导致重复现有的值。
- 无效的值将使整个声明无效，导致每个动画仅播放一次。

#### `animation-direction`

- `animation-direction`属性定义了动画通过关键帧的进行方向，有 4 种可能的值：
  - `normal`: 设置`normal`或省略（被默认设置为 `normal`）.动画的每一次重复都从 0%关键帧进行到 100%关键帧
  - `reverse`: 动画的每次重复按反向的关键帧的顺序，从 100%关键帧进行到 0%关键帧。**反转动画方向，也反转了`animation-timing-function`**
  - `alternate`: 动画在第一次和之后的奇数次重复时，从 0%关键帧进行到 100%关键帧。在第二次和之后的偶数次重复时，从 100%关键帧进行到 0%关键帧
  - `alternate-reverse`: 同`alternate`值相似，但是它是反向的。以下是一个球弹跳(球是从 将它仍下去开始，而不是把它抛向空中开始)的动画例子
    ```CSS
    .ball {
        animation-name: bouncing;
        animation-duration: 2500ms;
        animation-iteration-count: infinite;
        animation-direction: alternate-reverse;
    }
    @keyframes bouncing {
        from {
            transform: translateY(500px);
        }
        to {
            transform: translateY(0);
        }
    }
    ```
    ![ball_bouncing](/image/ball_bouncing.gif)

#### `animation-delay`

- 默认情况下，动画一应用到元素就开始重复动画，延迟 0 秒。
- 负值会导致动画立即开始，且在动画的中途开始。例如，如果一个动画`animation-delay: -4s`，`animation-duration: 10s`，动画会立即开始，在第一个动画的`40%`处开始，并将在`6s`后结束。
- 如果一个动画被设置重复 10 次，延迟为`-600ms`,单次动画持续时间为`200ms`，那么元素将在第 4 次重复时立即开始动画，且动画 1400ms。`（200ms x 10 = 2000ms）`

  ```CSS
  .ball {
    animation-name: bounce;
    animation-duration: 200ms;
    animation-delay: -600ms;
    animation-iteration-count: 10;
    animation-timing-function: ease-in;
    animation-direction: alternate;
  }

  ```

- 以上动画，重复 7 次，3 因为负值的`animation-delay`，有 3 次没有发生。`animationiteration`事件触发了 6 次，最后一次重复同`animationend`事件相同。**因为`animationiteration`和`animationend`事件不能同时触发，所以`animationiteration`事件不会触发**

#### 动画事件

有三种不同类型的动画事件：`animationstart`,`animationiteration`,`animationend`。每个事件都有三个只读的属性：`animationName`,`elapsedTime`,`pseudoElement`.`elapsedTime`的值是`animation-delay`值的绝对值。

- `animationstart`事件，在动画开始时触发，如果有动画延迟，则在延迟之后触发。如果延迟值为负值，则立即触发。
- `animationend`事件在动画完成时触发。如果`animation-iteration-count`设置为`infinite`，并且`animation-duration`设置的时间大于 0，`animationend`事件就永远不会触发。如果`animation-duration`值为 0，即使`animation-iteration-count`值为`infinite`，那么`animationstart`和`animationend`几乎会同时触发。
- `animationiteration`事件在动画重复之间触发，并且它与`animationend`不会同时触发。注意，当动画延迟为负值时，可能不会触发`animationiteration`事件。例如:
  ```CSS
  .noAnimationIteration {
      animation-name: change_color;
      animation-duration: 1s;
      animation-iteration-count: 4;
      animation-delay: -3s;
  }
  ```

#### `animation-timing-function`

`animation-timing-function`属性描述了动画将如何在其持续时间或一个重复周期内进行。其值可以是`step() 阶跃函数`和`贝塞尔曲线`

- `step()`,它不是线性渐变的，它是一种跳跃式的，主要用在`sprite`动画中。
  - 第一个参数，必须正整数，表示动画过程被平均分成的步骤数
  - 第二个参数，可选，接受`start`和`end`,默认`end`.指定在每个间隔的起点或终点发生阶跃变化。

#### `animation-play-state`

`animation-play-state`属性定义动画是运行(`runing`)还是暂停(`paused`)。

- 如果在重复中停止，则动画过程中的属性将保持在重复中的值，当设置为`runing`或返回到默认运行时，从停止的地方重新启动。

#### `animation-fill-mode`

`animation-fill-mode`属性定义了动画时如何影响元素的，是在`animationstart`触发之前，`animationend`事件触发之后 设置元素的属性值。

- `none`:默认值，即动画中的更改仅在动画执行期间，在不执行时没有效果。一旦动画完成，这些值都将恢复到动画之前的值。如果`animation-delay`值为正数，则动画不会立即影响元素的属性值，而是在过了延迟时间后，`animationstart`事件触发时。
- `backwards`: `0%`或`from`关键帧的属性值立即应用到元素，不需要等待动画延迟的时间
- `forwards`: 动画执行完成后，元素依旧使用动画完成时的属性值。
- `both`:集合了`backwards`和`forwards`.
- 如果`animation-iteration-count`是一个浮点数，不是一个整数。最后重复将不会在`0%`或`100%`关键帧上结束，动画将在中途结束执行。如果此时`animation-fill-mode`的值为`both`或`forwards`,则元素将维持`animationend`事件触发时的属性值。
  ```CSS
  @keyframes move {
      0% {
          transform: translateX(0);
      }
      100% {
          transform: translateX(1000px);
      }
  }
  .moved {
      transform: translateX(0);
      animation-name: move;
      animation-duration: 10s;
      animation-timing-function: linear;
      animation-iteration-count: 0.6;
      animation-fill-mode: forwards;
  }
  ```
  - 此动画重复 0.6 次，作为一个线性过渡的 10s 动画，它将在 60%处停止，即在进入动画 6s，元素被向右平移 600px 时。因为`animation-fill-mode: forwards`，所以该元素属性值保持不变,`transform: translateX(600px)`

### 综合范例

```animation
Values:         [ animation-name || animation-duration || animation-timing-function ||
                animation-delay  || animation-iteration-count || animation-direction ||
                animation-fill-mode || animation-play-state ]
Initial valueL  'name' 0s ease 0s 1 normal none running
```
