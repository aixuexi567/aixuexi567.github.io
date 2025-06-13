---
title: CSS知识点梳理(16)-转换（Transform）
categories: 技术总结
date: 2020-01-04 21:13:25
tags: css
---

### 坐标系统

- 笛卡尔坐标系统（Cartesian coordinate systems）

  - 该系统就是常见`x/y/z`坐标系统，`x`代表水平轴，`y`代表垂直轴,`z`代表深度轴。`x`正值向右，负值向左,`y`正值向下，负值向上,`z`正值离你更近，负值离你更远。
  - **每个元素都携带它自己的参照系，也就是说，如果旋转一个元素，那么它的轴也会随之旋转，之后的变换都是对那些旋转的轴进行计算的**
    ![Elemental frames of reference](/image/elemental_frames_of_reference.jpg)

- 球面坐标系统 (spherical coordinate systems)
  球面坐标系统描述了三维空间中的角度。对于 2D 中变换，只需要考虑一个单独的 360° 极坐标系统，它位于 x,y 轴描述的平面上。

  - 对于 2D 旋转，实际上描述了绕`z`轴的旋转(在 xy 平面上旋转)。**正度数，顺时针旋转，负度数，逆时针旋转**
    ![Rotations in the xy plane](/image/rotation_z.jpg)

  - 绕 `x`轴的旋转使元素向你倾斜或远离你,绕`y`轴的旋转使元素从一边转向另一边。  
    ![Rotations around the three axes](/image/rotation_three_axes.jpg)

### 转换

- 百分比值相对于`boundiing box`,在 CSS 中，它就是`border box`，忽略轮廓和边距。
- 所有转换后的元素有自己的堆栈上下文。**元素在页面上占用的实际空间与转换之前相同**
- 转换函数是一个接一个的处理的，所有其顺利不同，结果可能有所不同。
- 在一系列转换函数中，必须所有的函数是正确格式，如果其中一个函数无效，则整个`transform`的值都无效。
- 转换通常不是累加的，但是动画转换，无论是使用过渡（`transition`）还是实际的动画(`animation`)，是可以叠加的。
- 在没有显示的指定过渡或动画，可以通过伪类(例如`:hover`)来创建可叠加的转换。·
  ```CSS
  img#one {
      transform: translateX(100px) scale(1,2);
  }
  img#one:hover{
      transform: rotate(-45deg);
  }
  ```
- 转换不能应用到原子内联级的框中，例如`span`,`a`，可以通过`display:block;`,`display:inline-block`来改变显示角色。

#### translate 函数

|          Function          |                                                 Permitted value                                                  |
| :------------------------: | :--------------------------------------------------------------------------------------------------------------: |
| translateX(), translateY() |                                   &#60;length&#62; &#124; &#60;percentage&#62;                                   |
|        translate()         |          [&#60;length&#62; &#124; &#60;percentage&#62;][,&#60;length&#62; &#124; &#60;percentage&#62;]?          |
|        translateZ()        |                                                 &#60;length&#62;                                                 |
|       translate3d()        | [&#60;length&#62; &#124; &#60;percentage&#62;],[&#60;length&#62; &#124; &#60;percentage&#62;],[&#60;length&#62;] |

![Translating in two dimensions](/image/translate_in_dimensions.jpg)

- 百分比的值，其距离计算是相对于元素自身大小。
- `translate(x,y)`如果`y`值省略，则它被设置为`0`，`translate(2em)`等价于`translate(2em, 0)`
- `translateZ()`的值，仅接受长度值，不允许使用百分比值。
- `translate3d()`必须包含 3 个值，`translate3d(1em, -50px)`视为无效，不会被认为是`translate3d(1em, -50px, 0)`

#### scale 函数

|           Function           |                  Permitted value                   |
| :--------------------------: | :------------------------------------------------: |
| scaleX(), scaleY(), scaleZ() |                  &#60;number&#62;                  |
|           scale()            |        &#60;number&#62;[,&#60;number&#62;]?        |
|          scale3d()           | &#60;number&#62;,&#60;number&#62;,&#60;number&#62; |

![Scaled elements](/image/scale_in_demensions.jpg)

- `scale`函数只接受数值，不接受百分比的值。
- `scale()`,如果只提供一个数字，它将被用作两个轴的缩放值。`scale(2)`等价于`scale(2, 2)`.

#### rotate 函数

|                 Function                  |                          Permitted value                           |
| :---------------------------------------: | :----------------------------------------------------------------: |
| rotate(), rotateX(), rotateY(), rotateZ() |                          &#60;angle&#62;                           |
|                rotate3d()                 | &#60;number&#62;,&#60;number&#62;,&#60;number&#62;,&#60;angle&#62; |

![Rotations around the three axes](/image/rotation_three_axes.jpg)

- `rotate()`是一个 2D 旋转，等价于`rotateZ()`，围绕 z 轴旋转。
- `rotate3d(x,y,z,angle)`是围绕所声明的三维向量旋转。 x,y,z 表示向量 在 x,y,z 轴的分量，angle 表示 旋转量。
  ![Rotations aroud 3D vector](/image/rotation_vectors.jpg)

- `rotate3d(1,1,0,45deg)`的结果不等于`rotateX(45deg) rotateY(45deg) rotateZ(0deg)`  
  ![The difference between rotating around two axes and rotating around a 3D axis](/image/float_interact_differently_with_blocks_and_grids.jpg)

#### skew 函数

|    Functions     |           Permitted value           |
| :--------------: | :---------------------------------: |
| skewX(), skewY() |           &#60;angle&#62;           |
|      skew()      | &#60;angle&#62;[, &#60;angle&#62;]? |

![Skewing along the x,y axes](/image/skew_along_x_y_axes.jpg)

- `skew(a,b)`同`skewX(a) skewY(b)`的行为不同，它是用矩阵运算`[ax,ay]`来指定 2D 倾斜的。
  ![Skewed emelents](/image/skew_element.jpg)

- `skew(x,y)`如果忽略了 y 值，那么 它就是 0.

#### perspective 函数

`perspective(<length>)`，用于指定观察点到 元素的距离。

- 观察点距元素越近，元素的变形越大。
- 此属性在舞台元素上设置，对进行 3D 变换的子元素生效。如果子元素非 3D 变换元素，则没有任何效果。
- 透视效果，近大远小
- 观察点/视点的位置可以结合`perspective-origin`属性进行调整。

  ![The effects of varying perspective values](/image/effect_of_varying_perspective_values.jpg)

#### matrix 函数

关于矩阵，比较复杂，且现阶段较少用到，故跳过该节。

### 更多转换属性

#### 原点改变

- 所有的转换默认使用元素中心作为转换远点。
- `transform-origin`，使用 2 或者 3 个关键字定义转换原点。水平的，垂直的，可选择的 Z 轴长度。
- 百分比值是根据元素大小计算的。

  ![Various origin calculations](/image/various_origin_calulation.jpg)

- 改变原点对旋转的影响

  ![The rotational effects of using various transform origins](/image/rotational_effects_origins.jpg)

- 改变原点对倾斜的影响

  ![the skew effects of using varous transform origins](/image/skew_effects_origins.jpg)

#### 3D 样式

- `transform-style`属性指定了嵌套元素如何在 3D 空间中呈现。`flat`默认值，表示所有子元素在 2D 平面呈现，`preserve-3d`表示所有子元素在 3D 空间中呈现。
- `transform-style`属性需要设置在父元素中，**它可以被其他属性覆盖，这是因为其他属性的一些值需要元素及其子元素在平面上才能正常工作。因此，为了避免这种覆盖行为，以下属性必须设置为以下值**

  - overflow: visible
  - filter: none
  - clip: auto
  - clip-path: none
  - mask-image: none
  - mask-border-source: none
  - mix-blend-mode: normal

- `flat`变换的效果

  ```CSS
      div#outer {transform: perspective(750px) rotateY(60deg) rotateX(-20deg);}
      div#inner {transform: perspective(750px) translateZ(60px) rotateX(45deg);}
  ```

  ![the effects of a flat transform style](/image/flat_transform.jpg)

- `preserve-3d`变换的效果
  ```CSS
      div#outer {transform: perspective(750px) rotateY(60deg) rotateX(-20deg); transform-style: preserve-3d;}
      div#inner {transform: perspective(750px) translateZ(60px) rotateX(45deg);}
  ```
  ![the effects of a 3d-preserved transform style](/image/preserve_3d_transform.jpg)

#### 视角转换

- `perspective`属性，应用于父元素，规定其所有子元素的透视深度(**Z 平面距离用户之间的距离**)；`perspective()`函数，只定义了给定该函数的元素的透视深度。

  ```CSS
  div {transform-style: preserve-3d; border:1px solid gray; width: 660px}
  img {margin: 10px;}
  #one {perspective: none};
  #one img {transform: perspective(800px) rotateX(-50grad);}
  #two {perspective: 800px;}
  #two img {transform: rotateX(-50grad);}

  <div><img src="rsq.gif"><img src="rsq.gif"><img src="rsq.gif"></div>
  <div id="one"><img src="rsq.gif"><img src="rsq.gif"><img src="rsq.gif"></div>
  <div id="two"><img src="rsq.gif"><img src="rsq.gif"><img src="rsq.gif"></div>
  ```

  ![Shared perspective versus individual perspectives](/image/shared_perspective_vs_individual_perspectives.jpg)

- `perspective-origin`定义了观察者视角相对于元素的位置（视线灭点），默认是元素的中心位置。其值同`transform-origin`相似。例如`perspective-origin: left top;`，即其视线灭点在`左上角`

#### 背面处理

`backface-visibility`属性指定当元素背面朝向观察者时是否可见(`hidden`,`visible`)。

- 在 3D 空间，旋转时，默认值，背面是可见的。
- 例子，[悬浮翻面]

  ```CSS
    section {position: relative;}
    img, div {position: absolute; top: 0; left: 0;}
    div {transform: rotateY(180deg); backface-visibility: hidden;
    background: rgba(255,255,255,0.85);}
    section:hover {transform: rotateY(180deg); transform-style: preserve-3d;}


    <section>
    <img src="photo.jpg" alt="">
    <div class="info">(…info goes here…)</div>
    </section>
  ```

  ![Photo on the front, information on the back](/image/backface_visibility_example.jpg)
