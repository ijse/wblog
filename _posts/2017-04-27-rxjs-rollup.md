---
ID: 187
post_title: RxJS 入门记录
author: ijse
post_date: 2017-04-27 18:33:47
post_excerpt: ""
layout: post
permalink: https://ijser.cn/rxjs-rollup/
published: true
---
最近项目由于业务复杂度的不断提升，前端页面的数据层管理有些混乱和低效，出现数据更新同步复杂，数据源众多等问题。

vuex 对于一般复杂的前端组件共享数据是没什么问题的，但是当后端数据源复杂，数据需要进行复杂的合并、关联计算、预处理时，mutation 和 action 就会写得愈加复杂，同时团队成员也可能由于水平问题及对业务的不同角度理解，容易造成数据层混乱，使接下来新业务功能的开发越来越复杂，代码慢慢变得维护困难。

在这种背景下，似乎 RxJS 是不错的解决方案。

<!--more-->
<h2>问题</h2>
以下是我们在处理数据层时希望能够被解决好的问题：
<ul>
 	<li>异步的数据源</li>
 	<li>多种数据关联计算</li>
 	<li>需要最小范围更新（精准更新）</li>
 	<li>定时触发更新</li>
</ul>
一个较理想的方式：我们把数据抽象包装为数据线，然后像接线一样将不同数据线连接到我们的处理模块及控制器上；当数据线上有数据通过时，直接推送到我们数据消费方，或者经过控制器，有选择地推送。

这其实就是消息订阅，是观察者模式的一种实现。
<h2>准备</h2>
官网：http://reactivex.io/rxjs

Dash Docset: https://github.com/royling/rxjs.docset
<p id="JuCraDs"><img class="alignnone size-full wp-image-191 " src="https://ijser.cn/wp-content/uploads/2017/04/img_5901888379484.png" alt="" /></p>

<h2>V4 vs V5</h2>
GitHub中主要有两个RxJS版本实现，分别是：
<ul>
 	<li>V4： https://github.com/Reactive-Extensions/RxJS</li>
 	<li>V5：https://github.com/ReactiveX/rxjs
<p id="wlHVZCg"><img class="alignnone size-full wp-image-193 " src="https://ijser.cn/wp-content/uploads/2017/04/img_5901c770e13e6.png" alt="" /></p>
</li>
</ul>
V5是最新的版本，二者在思想方面是一致的，V5版本主要优点是：
<ul>
 	<li>性能更好</li>
 	<li>调用堆栈输出更简洁，更易于调试</li>
</ul>
详细对比：https://github.com/ReactiveX/rxjs/blob/master/MIGRATION.md

所以，现在更倾向使用V5版本。
<blockquote>使用npm安装RxJS 5: <code>npm install rxjs</code></blockquote>
<h2>梳理</h2>
<blockquote>RxJS提供了各种API来创建数据流：
<ul>
 	<li>单值：of, empty, never</li>
 	<li>多值：from</li>
 	<li>定时：interval, timer</li>
 	<li>从事件创建：fromEvent</li>
 	<li>从Promise创建：fromPromise</li>
 	<li>自定义创建：create</li>
</ul>
创建出来的数据流是一种可观察的序列，可以被订阅，也可以被用来做一些转换操作，比如：
<ul>
 	<li>改变数据形态：map, mapTo, pluck</li>
 	<li>过滤一些值：filter, skip, first, last, take</li>
 	<li>时间轴上的操作：delay, timeout, throttle, debounce, audit, bufferTime</li>
 	<li>累加：reduce, scan</li>
 	<li>异常处理：throw, catch, retry, finally</li>
 	<li>条件执行：takeUntil, delayWhen, retryWhen, subscribeOn, ObserveOn</li>
 	<li>转接：switch</li>
</ul>
也可以对若干个数据流进行组合：
<ul>
 	<li>concat，保持原来的序列顺序连接两个数据流</li>
 	<li>merge，合并序列</li>
 	<li>race，预设条件为其中一个数据流完成</li>
 	<li>forkJoin，预设条件为所有数据流都完成</li>
 	<li>zip，取各来源数据流最后一个值合并为对象</li>
 	<li>combineLatest，取各来源数据流最后一个值合并为数组</li>
</ul>
（from: https://github.com/xufei/blog/issues/44)</blockquote>
上面只是举例，实际RxJS提供了非常多的方法。

这里有一个非常棒的在线工具，将每个方法用marbles图画演示出来：
<p id="fjcWFRQ"><img class="alignnone size-full wp-image-200 " src="https://ijser.cn/wp-content/uploads/2017/04/img_5901e60e51254.png" alt="" /></p>
http://rxmarbles.com/
<h2>调试</h2>
在Devtools中调试异步代码通常都比较痛苦，目前可能最有效的方法仍是<code>console.log:</code>

添加`<code class="highlighter-rouge">.do(x =&gt; console.log(x))`在合适的结点。</code>

另外， 还可以自己画流程图 或者 珠宝图。方法都比较原始，可能在阅读和调试别人的RxJS代码时仍会比较麻烦吧。

参考：https://staltz.com/how-to-debug-rxjs-code.html

&gt; 有一个在线可视化调试工具：http://jaredforsyth.com/rxvision/，可惜作者已经不怎么维护了，并且它并不支持最新的RxJS V5版本。
<h2>资源</h2>
官网的Manual就是非常不错的入门教程：http://reactivex.io/rxjs/manual/overview.html

另外，其它一些资源可以从awesome-rxjs上去找。