---
title: D3.js入门
tags: js
---
本文主要参考[D3.js入门教程](http://pkuwwt.gitcafe.com/d3-tutorial-cn/about.html)和[d3js.org](http://d3js.org/)，作为自己读书笔记。
<!--more-->
# 目录内容

[TOC]

# 基础
## HTML
*HTML*(超文本标记语言，HyperText Markup Language)用于结构化网页内容。(作者以初学者的视角写的，又让我想起大一自己看html那笨样子，作者写的很简明所以保留下来)
```html
<html>
<head>
    <title>Page Title </title>
</head>
<body>
    <h1>Page Title </h1>
    <p>This is a really interesting paragraph.</p>
</body>
</html>
```
## DOM
文档对象模型(Document Object Model, DOM)指的是HTML的分层结构。我们用人类术语描述元素之间的相对关系:父亲，儿子，兄弟。
## CSS
层叠样式表(Cascading Style Sheets, CSS)用于为HTML页面的图形表达设计样式。  
CSS样式包括选择器(selectors)和规则(rules)。选择器指定应用样式的元素，下面示例：  
* `h1`:选择第1层标题
* `p`:选择段落
* `.caption`:选择`class`为`caption`的元素
* `#subnav`:选择`ID`为`subnav`的元素
选择器和规则之间用大括号连接。  
```html
p{
    font-size: 12px;
    line-height: 14px;
    color: black;
}
```
D3使用CSS-样式选择器来定位需要操作的那些元素，因此理解CSS的使用很重要。

CSS的规则可以直接放在网页的`head`中，或生成单存于后缀为.css的外部文件中，然后在网页`head`中引用这个文件。
```html
<head>
  <link rel="stylesheet" href="style.css">
</head>
```
## Javascript
Javascript是一种动态脚本语言，它可以想浏览器发送命令，在网页完成加载之后再去修改网页内容。

脚本可以直接放在HTML中间的两个`script`标签之间。
```html
<body>
<script type="text/javascript">
alert("Hello, world");
</script>
</body>
```
也可以存于外部文件中，然后在HTML的某个位置上引用(一般也是`head`)。
```
<head>
<title>Page Title <title>
<script type="text/javascript src="myscript.js"></script>
</head>
```
## SVG
D3最擅长用于可缩放矢量图形(Scalable Vector Graphics, SVG)来渲染可视化结果。SVG是一种基于文本的图像格式。
```html
<svg>
<circle cx="25" cy="25" r="22" fill="blue" stroke="gray" stroke-width="2"/>
</svg>
```
SVG提供大量的可视化效果，常规的HTML元素根本无法实现。
更多的基础知识可参考[w3school](http://www.w3school.com.cn/)
# 安装
## 下载D3
为你的D3工程创建一个新目录，如d3的子目录。然后将最新的[d3.js](https://github.com/mbostock/d3)下载到此目录中。可以放在`head`标签内。
``` javascript
<script type="text/javascript" src="d3/d3.js"></script>
```
或者直接引用
```javascript
<script type= src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
```
# Let’s Make a Bar Chart
## Selecting an Element(选择一个元素)
典型的处理元素如下，创建`div`元素，设置它的内容，把它加入到`body`里面。
```javascript
var div = document.createElement("div");
div.innerHTML = "Hello, World!";
document.body.appendChild(div);
```
用D3(例如jQuery 和其他的库)，可以省去循环，是代码更简洁。
```javascript
var body = d3.select("body");
var div = body.append("div");
div.html("Hello, world!");
```
你可以很容易的将同样的操作应用到多个元素上：
```javascript
var section = d3.selectAll("section");
var div = section.append("div");
div.html("Hello World")
```
## Chaining Methods
D3巧妙的利用*链式语法(chain syntax)*,眼尖的读者可以看到jQuery的痕迹。链式语法使语法更简洁。
使用链式语法有一点你需要明白：很多操作返回相同的选择器，有些方法返回一个新的选择器。例如`selection.append`返回一个新的选择器选中的元素。这很方便的允许你用链式方法操作新的元素。
```javascript
d3.selectAll("section")
   .attr("class", "special")
   .append("div")
   .html("Hello, world");
```
因为链式方法只能用于逐步深入的解析元素，用`var`可以关联选择器，用于回退。
```javascript
var section = d3.selectAll("section");
section.append("div")
        .html("First!");
section.append("div")
        .html("Second.");
```
## Coding a Chart, Automatically
对于硬编码(将数据写死)对于其他数据是不实用的，现在我们用数据动态生成图表。在一个空的页面中只有`div`和他的css类`chart`。
```javascript
d3.select(".chart")
  .selectAll("div")
  .data(data)
  .enter().append("div")
  .style("width", function(d){return d*10 + "px";})
  .text(function(d){return d;});
```
`.selectAll("div")`:找到DOM中所有`div`，将其引用传递给链中的下一个方法。

`data(data)`: 对数值进行计数和解析。数据集中有6个数据，将会执行六次，分别对用6个的值。

`.enter()`: 为了生成新的绑定了数据的元素，你必须用`enter()`方法。此方法比较DOM元素和待处理数据的个数。如果数据的个数多于相应的DOM元素，则`enter()`方法会生成*新的占位元素*,在每个占位元素上进行余下的操作。每个占位元素都会被传递给链中下一个方法。

`.append("div")`: 接收有`enter()`选择的(新生成的)点位元素，然后在DOM中插入`div`元素。

`.style()`: 设置新生成元素的样式，调用函数`function`,返回柱状图的宽度。

`.text()`: 接收新生成的`div`引用，然后插入文本。
## Scaling to Fit (伸缩适应)
上面的代码中的10是魔数。图表的可伸缩型的范围是数值范围的10倍。
我们可以用[linear scale](https://github.com/mbostock/d3/wiki/Quantitative-Scales)。消除魔数的存在。D3的伸缩范围映射到数据域的范围(domain)到展示域(range):
```javascript
var x = d3.scale.linear()
        .domain([0, d3.max(data)])
        .range([0, 420]);
```
尽管`x`看起来像一个对象，它其实是个函数`function`。它可以返回数据域的数值所对应展示域的数值。
```javascript
d3.select(".chart")
  .selectAll("div")
  .data(data)
  .enter().append("div")
  .style("width", function(d){return x(d) + "px";})
  .text(function(d){return d;});
```
## Using SVG Coding a Chart
下面我们用D3动态创建一个柱状图
```
<!DOCTYPE html>
<meta charset="utf-8">
<style>
    .chart rect{
        fill: steelblue;
    }
    .chart text{
        fill: white;
        font: 10px sans-serif;
        text-anchor: end;
    }
</style>

<svg class="chart"></svg>
<script src="d3/d3.js"></script>
<script>
    var data=[4, 8, 15, 16, 23, 42];
    var width = 420,
    barHeight = 20;
    var x = d3.scale.linear()
    .domain([0, d3.max(data)])
    .range([0, width]);

    var chart = d3.select(".chart")
        .attr("width", width)
        .attr("height", barHeight*data.length);

    var bar = chart.selectAll("g")
        .data(data)
        .enter().append("g")
        .attr("transform", function(d,i){return "translate(0,"+i*barHeight+")";});
        bar.append("rect")
        .attr("width", x)
        .attr("height", barHeight-1);

        bar.append("text")
        .attr("x", function(d){return x(d)-3;})
        .attr("y", (barHeight-1)/2)
        .attr("dy", ".35em")
        .text(function(d){return d;});
</script>
```
## Loading Data(加载数据)
为了能在浏览器中使用数据据，我们需要从服务器上加载数据文件并解析它。将文件中的文本转换成JavaScript的对象。幸运的是d3.tsv的一个函数可以完成这两项功能。
数据的加载是很复杂的流程：加载是异步(_asynchronous_)。当调用d3.tsv时它会立即得到返回，文件正在背后进行加载的。有些必须指出当加载完成时，你的调用函数返回新的数据，或者加载失败返回错误信息。
```
1. Code here runs first, before the download starts.
d3.tsv(“data.tsv”, function(error, data){
    //3. Code here runs last, after the download finishes.
});
//2. Code here runs second , while the file downloading.
```

