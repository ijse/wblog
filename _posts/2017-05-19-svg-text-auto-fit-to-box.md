---
ID: 220
post_title: 自动缩放文字以适应容器
author: ijse
post_date: 2017-05-19 21:20:01
post_excerpt: ""
layout: post
permalink: >
  https://ijser.cn/svg-text-auto-fit-to-box/
published: true
---
在一些需求场景中，给定一个固定尺寸的容器，里面文字尺寸随内容的长短自动调整尺寸，如下效果：
<p id="pDWHWzf"><img class="alignnone size-full wp-image-221 " src="https://ijser.cn/wp-content/uploads/2017/05/img_591eb340c4cdc.png" alt="" /></p>
如果容器尺寸及内容都是不确定的动态值，CSS3 并没有直接可以实现的属性，所以估计需要借助JS，一般思路是：
<ol>
 	<li>JS获取容器尺寸</li>
 	<li>JS获取文字长度，计算文字最大尺寸</li>
 	<li>调整文字显示</li>
</ol>
但如果容器尺寸长度是100%, 是动态适应父容器的，怎么办？ 重新计算？怎样获取文字显示尺寸？不同字体怎么办？

问题看起来挺棘手，又涉及到JS操作和计算，方案也不clean。

经过一番搜索，最后总结出了相对可能靠谱的两个方案：
<h1>SVG text方案</h1>
SVG 是很强大的方案，一些炫酷的视觉交互效果都可以用SVG来实现.

但是遗憾的是SVG对文字处理的支持不是很好，比如不支持wrap什么的，同时这种效果也是无法直接实现的，还是需要借助JS。（用foreignObject也不行，无论是CSS还是SVG，目前都无沅直接控制文字占用尺寸）

以下是示例代码：
<pre class="lang:js decode:true">const updateSize = function () {
  const svg = document.querySelector('svg')
  const text = document.querySelector('text')
  const [ svgWidth, svgHeight ] = [
    svg.getAttribute('width'),
    svg.getAttribute('height')
  ]
  const { width, height } = text.getBBox()
  const ratio = Math.min(svgWidth / width, svgHeight / height)
  const fontSize = 100 * ratio + '%'
  text.setAttribute('font-size', fontSize)
}</pre>
JS代码获取SVG和TEXT的尺寸，然后对fontSize做调整。如果不希望文字太满，也可以将100替换为其它值，如90。

相应的SVG代码：
<pre class="lang:xhtml decode:true">&lt;svg width="350" height="120"&gt;
  &lt;text fill="#000" x="50%" y="50%"
        text-anchor="middle"
        alignment-baseline="central"&gt;
    Sample
  &lt;/text&gt;
&lt;/svg&gt;</pre>
这是最简代码，其中<code>text-anchor</code>和<code>alignment-baseline</code>属性用于对text文字的定位进行调整，使其居中显示。

当SVG的尺寸变化时，需要重新调用<code>updateSize()进行重绘，当然，也可以通过监听DOMSubtreeModified事件实现当text变化时自动调用。</code>

<img class="size-full wp-image-223 aligncenter" src="https://ijser.cn/wp-content/uploads/2017/05/img_591ef02208660.png" alt="" />
<p id="kxzamgw"><img class="size-full wp-image-224 aligncenter" src="https://ijser.cn/wp-content/uploads/2017/05/img_591ef041ba204.png" alt="" /></p>

<h1>Canvas 方案</h1>
这种方案理论上也是可以实现，但相比SVG方案也并不会简单太多。思路大致是：
<ol>
 	<li>JS获取容器尺寸</li>
 	<li>准备画布，计算位置和文字尺寸</li>
 	<li>画文字</li>
</ol>
这种方案也有缺点：文字无法选中和复制，放大可能会有模糊等问题，以及无法较方便地响应交互。