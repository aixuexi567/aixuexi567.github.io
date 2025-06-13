---
layout: w
title: EventLoop梳理
date: 2019-12-02 19:10:50
tags: javascript
---

JS 是单线程的，它是通过事件队列（`EventLoop`）的方式来实现异步回调的。

#### 浏览器内核(渲染引擎: Rendering Engine)

浏览器内核其实指的是浏览器内的一个进程，它负责页面的渲染、脚本执行、事件处理等，每一个 Tab 页一个渲染进程。  
对于渲染进程来讲，它是多线程的，包括以下线程

- GUI 渲染线程
  - 复杂渲染页面，回流、重绘
  - 与 JS 引擎线程互斥，防止渲染结果不可预期
- JS 引擎线程
  - 复杂处理解析、执行 JS 脚本程序
  - 只有一个 JS 引擎（单线程）
  - 与 GUI 渲染线程互斥
- 事件触发线程
  - 控制事件循环（鼠标点击、setTimeout、ajax 等）
  - 当事件满足触发条件时，将回调事件放入到 JS 引擎所在的执行队列中
- 定时器触发线程
  - setTimeout、setInterval 中计时
  - 计时完成后，通知`事件触发线程`
- 异步 HTTP 请求线程

  - 浏览器中一个单独的处理 AJAX 请求的线程
  - 当请求完成时，若有回调函数，通知`事件处理线程`

**为了防止渲染结果不可预期，浏览器设定`GUI渲染线程`和`JS引擎线程`互斥，当`JS引擎线程`执行时`GUI渲染线程`挂起，GUI 更新则会在`JS引擎线程`空闲时立即被执行**

#### JS 的运行机制

- JS 分为同步任务和异步任务
- 同步任务在`JS引擎线程`上执行，形成一个`执行栈`，且`JS引擎线程`只执行`执行栈`中的事件
- `事件触发线程`管理一个`任务队列`，异步任务触发条件达成，将回调事件放入`任务队列`中
- `事件队列`中的回调事件，是由各自线程插入到事件队列中的
- `执行栈`中所有同步任务执行完毕，此时`JS引擎线程`空闲，系统会读取`任务队列`，将可执行的异步任务回调事件添加到`执行栈`中，并执行

##### 举例说明

- setTimeout 定时任务
  当代码执行到 setTimeout 时，`JS引擎线程`通知`定时器线程`，间隔一段时间后，触发一个回调事件。而`定时器线程`在接受到这个消息后，在间隔时间后，将回调事件放入由`事件触发线程`所管理的`事件队列`中。
- ajax 网络请求
  代码执行到 ajax 时，`JS引擎线程`通知`异步HTTP请求线程`，发送一个网络请求，并制定一个请求完成后的回调事件。而`异步HTTP请求线程`接受到这个消息后，会在请求成功后，将回调事件放入由`事件触发线程`管理的`事件队列`中

**setTimeout， ajax 代码执行时，其本身是同步任务，而其中的回调函数才是异步任务。当同步任务执行完，`JS引擎线程`会询问`事件触发线程`，在`事件队列`中是否有待执行的回调函数，如果有的话，则加入到`执行栈`中，交给`JS引擎线程`执行**

#### 宏任务、微任务

我们可以将每次执行栈执行的代码当做一个宏任务(`包括每次从事件队列中获取一个事件回调并放入执行栈中执行`)，每一个宏任务会从头到尾执行完毕，期间不会执行其他。  
因为`JS引擎线程`和`GUI渲染线程`互斥，浏览器为了能够是`宏任务`和`DOM任务`有序的进行，会在一个`宏任务`执行结束后，在一个`宏任务`执行前，`GUI渲染线程`开始工作，更新 DOM、对页面进行渲染。

**主代码，setTimeout, setInterval 等，属于`宏任务`**

微任务可以理解为：在当前`宏任务`执行后立即执行的任务。也就是说，在`宏任务`执行完，在`渲染`前，将执行期间所产生的所有`微任务`。
**Promise, process.nextTick，mutation observer 等，属于`微任务`**

```JavaScript
//  宏任务 --> 所有微任务 ---> 渲染 ---> 宏任务 ---> 所有微任务 --->渲染 ...
```

#### 总结

- 执行一个`宏任务`(`执行栈`中没有，从`事件队列`中取)
- 执行过程中遇到`微任务`,将把它添加到`微任务队列`中
- `宏任务`执行完毕后，立即依次执行当前`微任务队列`中的所有`微任务`
- 当前`宏任务`执行完毕，开始检查渲染，然后`GUI渲染线程`接管渲染
- 渲染完毕后，`JS引擎线程`继续接管，开始下一个`宏任务`（从事件队列中获取）
  ![EventLoop](/image/EventLoop.png)

---

---

---

### 宏任务/微任务（再总结）

以下内容参考了该文章: [任务、微任务、队列和调度 (英文版)](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
**微任务执行时机（2 种情况）：**

- after every callback, as long as no other JavaScript is mid-execution （在每个回调之后，没有其他 JavaScript 在执行时）
- at the end of each task （在每个宏任务执行完之后）

#### 代码触发事件 / 鼠标、键盘等操作触发事件

- 鼠标、键盘等操作触发的事件，事件在 JS 执行栈中执行完后，执行栈为空，可执行加微任务。因冒泡触发其他事件加入执行栈，再次执行。
- 代码触发的事件，会先在 JS 执行栈执行`script`代码,再添加触发事件，触发事件执行完后，因 JS 执行栈还在执行`script`代码，故不可执行微任务。接着冒泡触发的事件加入执行栈执行。当所有冒泡事件执行完，`script`执行完后，才执行微任务队列。

#### 示例

内外两个 div，都注册了鼠标单击事件，

```HTML
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
    <style>
      .outer {
        margin: 40px auto;
        width: 200px;
        height: 200px;
        display: flex;
        justify-content: center;
        align-items: center;
        background: chocolate;
      }
      .inner {
        width: 100px;
        height: 100px;
        background: navy;
      }
    </style>
  </head>
  <body>
    <div class="outer">
      <div class="inner"></div>
    </div>
    <script>
      let outer = document.querySelector(".outer");
      let inner = document.querySelector(".inner");

      new MutationObserver((mutations, observer) => {
        // console.log(mutations, observer);
        let dataSetRandomVal = mutations[0].target.dataset.random;
        console.log(`${dataSetRandomVal} - mutate`);
      }).observe(outer, { attributes: true });

      function onClick(event) {
        let currentClassName = event.currentTarget.className;
        console.log(`${currentClassName} - click`);

        setTimeout(() => console.log(`${currentClassName} - timeout`), 0);

        Promise.resolve().then(() =>
          console.log(`${currentClassName} - promise`)
        );

        outer.setAttribute("data-random", currentClassName);
      }

      inner.addEventListener("click", onClick);
      outer.addEventListener("click", onClick);

    //   outer.click();
    //   inner.click();

    </script>
  </body>
</html>

```

- 点击外/内层 div,鼠标单击触发

```JS

单击outer
demo.html:41 outer - click
demo.html:46 outer - promise
demo.html:36 outer - mutate
demo.html:43 outer - timeout

单击inner
demo.html:41 inner - click
demo.html:46 inner - promise
demo.html:36 inner - mutate
demo.html:41 outer - click
demo.html:46 outer - promise
demo.html:36 outer - mutate
demo.html:43 inner - timeout
demo.html:43 outer - timeout
```

![单击触发](/image/clickToTrigger.gif)

- 执行 outer.click() / inner.click()，代码触发

  ```JS

  outer.click();
  demo.html:41 outer - click
  demo.html:46 outer - promise
  demo.html:36 outer - mutate
  undefined   // outer.click()执行完，无返回值
  demo.html:43 outer - timeout

  inner.click()
  demo.html:41 inner - click
  demo.html:41 outer - click
  demo.html:46 inner - promise
  demo.html:36 outer - mutate    // MutationObserver的监听是异步触发，在所有的DOM操作完成后才触发使回调函数进入微任务队列，故这里触发一次
  demo.html:46 outer - promise
  undefined   // inner.click()执行完，无返回值
  demo.html:43 inner - timeout
  demo.html:43 outer - timeout
  ```

  ![代码执行触发](/image/codeToTrigger.gif)
