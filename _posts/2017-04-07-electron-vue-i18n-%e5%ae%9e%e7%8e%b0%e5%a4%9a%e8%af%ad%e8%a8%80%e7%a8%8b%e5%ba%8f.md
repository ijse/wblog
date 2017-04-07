---
ID: 160
post_title: Electron vue i18n 实现多语言程序
author: ijse
post_date: 2017-04-07 17:37:25
post_excerpt: ""
layout: post
permalink: 'https://ijser.cn/electron-vue-i18n-%e5%ae%9e%e7%8e%b0%e5%a4%9a%e8%af%ad%e8%a8%80%e7%a8%8b%e5%ba%8f/'
published: true
---
<h3>目标</h3>
<ul>
 	<li>在Main Process中实现多语言</li>
 	<li>在Renderer Process中，与Vue结合实现多语言</li>
 	<li>自动检测语言环境，亦可配置改变</li>
 	<li>动态加载多语言文件</li>
</ul>
<h3>实现</h3>
<!--more-->

i18next (http://i18next.com/)

在main Process中：
<pre class="lang:js decode:true ">import isDev from 'electron-is-dev'
import path from 'path'
import i18next from 'i18next'
import NodeFsBackend from 'i18next-node-fs-backend'
import LanguageDetector from 'i18next-electron-language-detector'

i18next
  .use(LanguageDetector)
  .use(NodeFsBackend)
  .init({
    debug: isDev,
    whitelist: [ 'en', 'zh' ],
    lowerCaseLng: true,
    load: 'all',
    fallbackLng: [ 'zh' ],
    ns: [
      'common'
    ],
    fallbackNS: 'common',
    backend: {
      loadPath: path.resolve(__dirname, '../resources/locales/{{lng}}/{{ns}}.json'),
      jsonIndent: 2
    }
  })

export default i18next
</pre>
程序启动时进行初始化，但加载是异步的，所以程序在启动初始化进程调用需要通过事件：
<pre class="lang:js decode:true ">i18n.on('loaded', () =&gt; {
  app.setName(i18n.t('appName'))
  console.log(i18n.t('appName'))
})
</pre>
在Renderer Process中使用，不需要再初始化，是共用同一个实例的：
<pre class="lang:js decode:true ">// i18n inject
window.i18next = remote.require('i18next')
</pre>
打通与Vue框架的集成：
<pre class="lang:js decode:true ">import VueParams from 'vue-params'
import VueI18Next from 'vue-i18next'

Vue.use(VueParams)
Vue.use(VueI18Next)

// change language at time
window.i18next.on('languageChanged', () =&gt; {
  console.log('change language', arguments)
  Vue.params.i18nextLanguage = window.i18next.language
})

</pre>
<code>vue-params</code>和<code>vue-i18next</code>两个依赖的代码很简单巧妙。前者保证了当语言切换时，所有<code>$t()及时更新；后者为Vue框架注入$t()和t()两个调用方法。</code>
<h3>最后</h3>
以上基本实现了需求，<code>i18next</code>功能很强大，并且还提供翻译服务，可以跟各种环境集成。

有关语言切换，如果处理得当，完全可以不需要重启程序。