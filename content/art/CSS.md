---
date: 2024-01-10T15:31:50+08:00
title: "CSS 基础"
description: "CSS is a language that describes the style of HTML documents"
tags: ["Web", "CSS"]
series: ["How to become a web developer"]
featured: true
---

# CSS

## 常见样式声明

## 选择器

## 层叠

## 继承

## 属性值计算过程

## 盒模型

> box: 盒子, 每个元素在页面中都会生成一个矩形区域(盒子)

### 盒子类型

- 行盒: `display`属性为`inline`的元素
- 块盒: `display`属性为`block`的元素

> 行盒在页面中不换行, 块盒独占一行

### 默认样式

- 浏览器默认样式表设置的块盒: 容器元素、h1 ~ h6、p
- 常见的行盒: `span、a、img、video、audio`

### 块盒盒模型

#### 盒子的组成部分

- 无论是行盒还是块盒, 都有一下几个部分组成, 由内至外分别是:
    1. 内容`content`: `width、height` 设置的是盒子内容的宽高, 内容部分通常叫做整个盒子的**内容盒 content-box**
    2. 填充`padding`: 盒子边框到盒子内容的距离`padding-top、padding-right、padding-bottom、padding-left`(
       简写: `padding: top、right、bottom、left`), 填充区 + 内容区 = **填充盒 padding-box**
    3. 边框`border`: 边框 = 边框样式`border-style` + 边框宽度`border-width` + 边框颜色`border-color`, 边框 + 填充区 +
       内容区 = **边框盒 border-box**
    4. 外边距`margin`: 边框到其他盒子的距离`margin-top、margin-right、margin-bottom、margin-left`(
       简写: `margin: top、right、bottom、left`)

#### 应用

##### 1、改变宽高范围

- 默认情况下, `width` 和 `height` 设置的是内容盒宽高
- 衡量设计稿尺寸时, 通常使用的是边框盒, 但设置`width`和`height`改变的是内容盒
    1. 精确计算
    2. CSS3: `box-sizing`, 即设置改变宽高的影响范围, ex: `box-sizing: border-box`

##### 2、改变背景覆盖范围

- 默认情况下, 背景覆盖边框盒, 可以通过`background-clip`进行修改, `background-clip`
  包含三种类型`border-box、content-box、padding-box`

##### 3、溢出处理

- `overflow`控制内容溢出边框盒后的处理方式
    - `visible`: 可见的
    - `hidden`: 隐藏
    - `scroll`: 生成滚动条(`overflow-x、overflow-y`可单独控制水平或垂直方向上的滚动条)

##### 4、断词规则

- `word-break`: 会影响文字的截断换行
    - `normal`: CJK 字符(China、Japan、Korea)在文字位置截断, 非 CJK 字符在单词位置截断
    - `break-all`: 所有字符均在文字位置截断
    - `keep-all`: 所有字符都在单词(空格)之间截断

##### 5、空白处理

- `white-space`: 空白折叠
    - `normal`: 进行空白折叠
    - `pre`: 拒绝空白折叠
    - `nowrap`: 发生空白折叠且不换行, 配合`overflow: hidden`, `text-overflow: ellipsis`可以对超出宽度的单行文本进行隐藏(
      仅支持单行文本)

### 行盒盒模型

- 常见的盒模型: 包含具体内容的元素, `span、strong、em、i、img、video、audio`

#### 显著特点

1. 盒子沿着内容延伸
2. 行盒不能设置宽高, 调整行盒的宽高应该使用字体大小、行高、字体类型进行间接调整
3. 内边距`padding`: 水平方向有效, 垂直方向仅影响背景, 不实际占据空间
4. 边框`border`: 水平方向有效, 垂直方向仅影响背景, 不实际占据空间

### 行块盒

- `display`属性为`inline-block`的盒子
- 不独占一行
- 盒模型中所有尺寸都有效

### 空白折叠

- 发生在行盒(行块盒)内部或行盒(行块盒)之间
- 换行书写行盒(行块盒)会产生间隙

### 可替换元素和非可替换元素

- 大部分元素, 页面上显示的结果, 取决于元素内容, 称为**非可替换元素**
- 少部分元素, 页面上显示的结果, 取决于元素属性(ex: src), 称为**可替换元素**

#### 可替换元素

- `img、video、audio`
- 绝大部分可替换元素均为行盒
- 可替换元素类似于行块盒, 盒模型中所有尺寸都生效

## 常规流

- 盒模型: 规定单个盒子的规则
- 视觉格式化模型: 页面中的多个盒子排列/布局规则
    1. 常规流
    2. 浮动
    3. (绝对)定位

### 常规流布局

- 常规流、文档流、普通文档流、常规文档流
- 所有元素, 默认情况系下, 都属于常规流布局
- 总体规则: 块盒独占一行, 行盒水平依次排列

#### 包含块(`containing block`)

- 每个盒子都有它的包含块, 包含块决定了盒子的排列区域(活动范围)。
- 绝大多数的包含块为其父元素的内容盒
- 每个块盒的总宽度, 必须刚好等于包含块的宽度

#### 块盒

1. 每个块盒的总宽度, 必须刚好等于包含块的宽度
    - 宽度默认值是`auto`, 吸收剩余空间, `margin`的取值也可以是`auto`, 默认为 0, width auto > margin auto
    - 若宽度、边框、内边距、外边距计算后, 仍然有剩余空间, 该剩余空间被`margin-right`全部吸收
    - 在常规流中, 块盒在其包含块中居中, 可以定宽, 然后左右`margin`为`auto`

2. 每个块盒垂直方向上的 `auto`
    - `height`: `auto`, 适应内容高度
    - `margin`: `auto`, 表示 0

3. 百分比取值
   > 宽度百分比
    - `padding、width、margin`可以百分比取值, 其所有百分比均相对于包含块的宽度, 与高度无关
   > 高度百分比
    - 包含块的高度取决于子元素的高度, 设置百分比无效
    - 包含块的高度不取决于子元素的高度, 百分比相对于父元素高度

4. 上下外边距合并
    - 两个常规流块盒, 上下外边距相邻, 会进行合并(使用`padding、border`可避免外边距合并)
    - 两个外边距取最大值

## 浮动

### 应用场景

- 文字环绕、横向排列

### 属性 - float

- `left`: 左浮动, 元素靠上靠左排列
- `right`: 右浮动, 元素靠上靠右排列
- 默认值为`none`

### 特点

1. 当一个元素浮动之后, 必定为块盒(更改`display`属性为`block`)
2. 浮动元素的包含块, 和常规流一样, 为父元素的内容盒

### 尺寸

1. 宽度为`auto`时, 适应内容宽度
2. 高度为`auto`时, 与常规流一致, 适应内容高度
3. `margin`为`auto`时, 为 0
4. 边框、内边距百分比设置与常规流一样

### 盒子排列

1. 左浮动的盒子靠上靠左排列
2. 右浮动的盒子靠上靠右排列
3. 浮动盒子在包含块中排列时, 会避开常规流块盒
4. 常规流块盒在排列时, 无视浮动盒子
5. 行盒在排列时, 会避开浮动盒子

> 如果文字没有在行盒中, 浏览器会自动生成一个行盒包裹文字, 该行盒叫做匿名行盒

### 高度坍塌

#### 根源

- 常规流盒子的自动高度, 在计算时, 不会考虑浮动盒子

#### 解决方案

- 清除浮动, 涉及`CSS`属性: `clear`
    - 默认值: `none`
    - `left`: 清除左浮动, 该元素必须出现在前面所有左浮动盒子的下方
    - `right`: 清除右浮动, 该元素必须出现在前面所有右浮动盒子的下方
    - `both`: 清除左右浮动, 该元素必须出现在前面所有浮动盒子的下方

##### 添加空元素

```html

<style>
    .item {
        width: 100px;
        height: 100px;
        background-color: red;
        margin: 10px;
    }
    .clearfix {
        clear: both;
    }
</style>

<body>
<div class="container">
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="clearfix"></div>
</div>
</body>
```

#### 添加伪元素选择器

```html

<style>
    .item {
        width: 100px;
        height: 100px;
        background-color: red;
        margin: 10px;
    }
    .clearfix::after {
        content: "";
        display: block;
        clear: both;
        float: left;
    }
</style>

<body>
<div class="container clearfix">
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
</div>
</body>
```

## 定位

- 手动控制元素在包含块中的精准位置
- 涉及`CSS`属性: `position`

### 属性

- `static`: 静态定位(不定位)
- `relative`: 相对定位
- `absolute`: 绝对定位
- `fixed`: 固定定位

> 一个元素, 只要`position`的取值不是`static`, 认为该元素是一个定位元素, 除相对定位以外的定位元素会脱离文档流
>> 1. 文档流中的元素摆放时, 会忽略脱离了文档流的元素
>> 2. 文档流中元素计算自动高度时, 会忽略脱离了文档流的元素

### 相对定位

- 不会导致元素脱离文档流, 只是让元素在原来位置上进行偏移
- 可以通过四个`CSS`属性对其位置进行偏移: `left、right、top、bottom`
- 盒子的偏移不会对其他盒子造成任何影响
- 为绝对定位的元素提供包含块

### 绝对定位

1. 宽高为`auto`, 盒子尺寸适应内容
2. 包含块变化: 寻找其所有父元素中的第一个定位元素, 该元素的填充盒为其包含块。若不存在, 则它的包含块为整个网页(
   初始化包含块)

### 固定定位

- 除包含块以外, 均与绝对定位相同
- 固定定位的包含块, 固定为浏览器的可视窗口(视口)

### 定位下的居中

- 某个方向居中
    1. 定宽/高
    2. 将左右/上下距离设置为 0
    3. 将左右/上下`margin`设置为`auto`
- 在绝对定位和固定定位中, `margin`为`auto`时, 会自动吸收剩余空间

### 多个定位元素重叠
- 堆叠上下文: 设置`z-index`, 通常情况下, 该值越大, 越靠近用户
- 只有定位元素设置`z-index`有效
- `z-index`可以是负数, 如果是负数, 则遇到常规流、浮动元素, 则会被其覆盖

### 补充
- 绝对定位、固定定位元素一定是块盒
- 绝对定位、固定定位元素一定不是浮动
- 没有外边距合并