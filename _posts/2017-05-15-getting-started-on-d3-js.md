---
ID: 209
post_title: Getting Started on D3.js
author: ijse
post_date: 2017-05-15 14:15:43
post_excerpt: ""
layout: post
permalink: >
  https://ijser.cn/getting-started-on-d3-js/
published: true
---
D3（Data-Driven Documents) 是一款基于SVG的非常基础、强大和应用广泛的库，提供了对于DOM结点操作的高效封装，适用于数据可视化的实现。

D3的核心库主要做了以下封装：
<ol>
 	<li>DOM选择器</li>
 	<li>DOM操作函数</li>
 	<li>动画变换</li>
</ol>
其实很像是jQuery，但与jQuery又有些根本的不同：

<!--more-->
<ul>
 	<li>D3 是数据驱动的，在D3中是通过操作改变数据从而改变DOM</li>
 	<li>D3 通常用于做数据可视化，并且有很多相关的可视化插件</li>
</ul>
由于都包含DOM操作库，所以一些API很像也不奇怪。
<blockquote>http://stackoverflow.com/questions/13187112/what-is-the-difference-between-d3-and-jquery</blockquote>
截止目前，最新版本是 D3 v4.8
<h1>D3优点</h1>
<ul>
 	<li>BSD开源，有庞大的社区支持和维护</li>
 	<li>数据驱动，以数据为核心，方便做数据到表现的映射</li>
 	<li>后缀语法、选择器、动态属性、数据绑定、动画；</li>
 	<li>大量示例，还有各种开源封装；</li>
 	<li>相对底层，可以方便实现各种定制需求</li>
 	<li>交互性实现相对于Canvas等简单；</li>
</ul>
<h1>D3缺点</h1>
<ul>
 	<li>由于主要基于SVG, 设计使用不当可能会造成DOM节点过多，产生性能问题</li>
 	<li>学习曲线较陡峭</li>
 	<li>相对于echarts, highcharts，实现图表较麻烦，工作量较大</li>
</ul>
所以，如果需求变化不大，echarts等demo图表就能够满足需求的话，建议直接echarts等这种现成图表库；如果需要定制化，则D3非常适合。
<h1>预备知识</h1>
<ul>
 	<li>HTML, CSS, JavaScript</li>
 	<li>DOM 概念和一般操作</li>
 	<li>SVG 标签及一些常用属性</li>
</ul>
作为前端开发者来说，D3只是又一个库而已。

但如果要做出优秀的数据可视化图表，还需要对各种图表特点和适用性，动画美学，源数据特点，以及物理公式等有一些了解。
<h1>学习工具</h1>
推荐用jsbin，jsfiddler等在线编辑器，可以方便预览。
<h1>参考：</h1>
<ul>
 	<li>https://github.com/d3/d3/wiki/Tutorials</li>
</ul>