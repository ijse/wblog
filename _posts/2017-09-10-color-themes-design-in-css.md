---
ID: 274
post_title: 多颜色主题设计
author: ijse
post_excerpt: ""
layout: post
permalink: >
  https://ijser.cn/color-themes-design-in-css/
published: true
post_date: 2017-09-10 17:43:44
---
如果要做一个有多种颜色主题（甚至可以支持自定义）的网站，我们可以为不同颜色主题分别写样式，但更好的方式是抽象出一套颜色体系，定义出颜色规范，确定几种元颜色值，不同颜色主题都通过这几种元颜色值按一定的变化方式产生，于是，多颜色主题的支持将会非常方便，并且其结果也将会令人满意，不会出现颜色值不协调、对比度不够等问题。

就像一个方程，给定特定几个参数，然后生成一套颜色主题。

而这也与通常设计的习惯相同，一般一套主题不会有特别多的色调，很多颜色都是相近或者是通过变换产生的。设计师们可能没有一种明确的计算方式，但凭他们的设计直觉会选出较合适的颜色值。
<h1>定义颜色类别</h1>
<h2>Primary Color 主要色</h2>
这是网站的主色调，承载着页面主题需要向用户传递情感的任务，一般是网站Logo主颜色（品牌色），或者也可能是出现频率最高，面积最大的区域颜色。

通常情况下这种颜色（或者其派生颜色）可以用在导航背景色、网站全局背景色、一些边框颜色、普通按钮颜色（及按钮选中、激活、失效状态）等。

有些情况下也可能需要多种Primary Color，可以再定义 Secondary Color。
<h2>Accent Color 辅助色</h2>
这种颜色是强调色，或者叫 衬托色，用于一些需要强调、引起用户特别关注的颜色，一般与Primary Color会有较强的对比反差，常用的如互补色，或者冷暖对比色。

通常用于 选中的文字背景色，悬浮按钮、执行操作按钮、进度条、选中的表单项高亮 等。

通常Secondary Color不会占比太多，一种较合适的颜色分布是： Primary : Secondary : Accent = 6 : 3 : 1
<h2 id="449c" class="graf graf--h4 graf-after--p">Semantic Colors 功能色</h2>
这种颜色是含有语义的，用于表示出错、失败、成功、警告等，通常分以下四种：
<ul>
 	<li>红色：代表 失败，出错，危险</li>
 	<li>黄色：代表 警告，注意，小心</li>
 	<li>绿色：代表 成功，安全，通行</li>
 	<li>蓝色：代表 普通中性提示信息</li>
</ul>
<h2 id="7ad0" class="graf graf--h4 graf-after--li">Neutrals 中立色</h2>
这种颜色是中性颜色，通常是 黑、白、灰，用于文字颜色、文章背景等，灰色是公认的安全色、百搭色，通常可以由另外两种颜色调配出来。
<h1>颜色变换规则</h1>
将以上几种颜色作为元颜色，其它所有颜色都从以上颜色中变换产生，于是接下来我们主要关注的是变换的方法，定义一套变化规则。

例如对于按钮，通常会有 默认状态、选中状态、禁用状态等：

<img src="https://ijser.cn/wp-content/uploads/2017/09/476.png" />

以上颜色都是从同一个颜色，通过变化 透明度、饱合度、对比度 等变换产生，这便是变化的规则。

定义变化的规则，应该着重注意识别度，不要让文字与背景不易被阅读。
<h1>定义元颜色</h1>
使用 CSS3 Variables 特性定义元颜色变量：
<pre class="lang:css decode:true">:root,
:root .theme-default {
  /* primary color */
  /* grey */
  --color-primary: #616a79;
  --color-primary-dark: #28323f;
  --color-primary-light: ##838d9e;

  /* accent color */
  /* yellow */
  --color-accent: #ffe100;

  /* sematic color */
  /* green for success, safe, right */
  --color-success: #78e397;
  /* orange for alert, caution, warning */
  --color-warn: #ff9529;
  /* red for error, danger, wrong*/
  --color-error: #ff4a4a;
  /* blue for information */
  --color-info: #218df2;

  /* neutrals */
  --color-light: #dddddd;
  --color-dark: #232c37;
}
:root .theme-night { ... }
:root .theme-day { ... }</pre>
定义出不同颜色主题元颜色后，便可以给 &lt;html&gt; 标签添加不同的class来改变页面主题。

注意：变量名称尽量不要用颜色名称，而是要用其代表的实际意义，抽象出其在样式主题中的意义。
<h1>进一步阅读</h1>
<ul>
 	<li>https://medium.com/@erikdkennedy/color-in-ui-design-a-practical-framework-e18cacd97f9e</li>
 	<li>http://www.jianshu.com/p/e662eb54c35e</li>
</ul>