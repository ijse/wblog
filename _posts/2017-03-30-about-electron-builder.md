---
ID: 151
post_title: Electron builder 打包的一些心得
author: ijse
post_date: 2017-03-30 17:36:08
post_excerpt: ""
layout: post
permalink: https://ijser.cn/about-electron-builder/
published: true
---
有关Electron 打包，网上有很多文章，但一些已经过时，并不适用于最新的版本，在研究的时候因此而走了一些弯路。

截止本文，我使用的相关版本是：
<ul>
 	<li>electron v1.6.4</li>
 	<li>electron-builder v16.4.1</li>
</ul>
大家可以根据情况阅读本文。

<!--more-->
<h3>两种主流的打包工具</h3>
官方文档中两种主要工具是electron-packager和electron-builder，区别主要是：
<ul>
 	<li>electron-packager主要是程序打包，并不包含生成Installer，且不能在Mac下打包Win程序</li>
 	<li>electron-builder是包含了electron-packager，能够生成Installer，并且可以在Mac平台下打包Mac, Linux和Windows包</li>
</ul>
electron-builder工具功能更强大，不仅支持打包，生成Installer，而且还可以发布到GitHub或S3等平台，且支持自动更新。
<h3>electron-builder的文档</h3>
文档地址：[link](https://github.com/electron-userland/electron-builder/wiki)

electron-builder代码提交很活跃，更新较频繁，文档也同样😔。

吐槽一下它的[Options](https://github.com/electron-userland/electron-builder/wiki/Options)文档，后半部分貌似是从代码注释中自动生成的文档，是根据代码类名排序的内容，而不是一般的理解和配置顺序，查看时候需要关注其配置项的名称和继承关系，并且有些链接是坏的。

它提供了两种配置方式，这里主要介绍通过<code>package.json这种方式。</code>

建议阅读的顺序是：
<ol>
 	<li>[Metadata](https://github.com/electron-userland/electron-builder/blob/master/docs/Options.md#Metadata) 这部分写的是Package.json中的配置项</li>
 	<li>[Config](https://github.com/electron-userland/electron-builder/blob/master/docs/Options.md#config--platformspecificbuildoptions) 这是在package.json#build中的配置</li>
</ol>
在阅读文档时，注意Type一栏列出的链接，然后再搜索找到相应的详细配置部分。（有的可能不在本页面内，可以在源码<code>packages/目录</code>中寻找）
<h3>OSX Signing</h3>
App的分发有两种形式：.app及 MAS。

app不签名默认是无法运行的。

我们需要购买一个Apple开发者账号，并本地生成一个密钥，然后导入证书。
<blockquote>refer：[link](https://github.com/nwjs/nw.js/wiki/MAS%3A-Requesting-certificates)</blockquote>
对于其它开发者，可以通过导出并分享.p12文件添加。
<h3>Windows Signing</h3>
<pre class="lang:sh decode:true" title="Signing for Windows">➜  my-app git:(master) ✗ makecert -sv my_win_private_key.pvk -n "CN=MyWinCertificate" -b 01/01/2017 -e 01/01/2027 -r my_win_certificate.cer
Mono MakeCert - version 4.6.2.0
X.509 Certificate Builder
Copyright 2002, 2003 Motus Technologies. Copyright 2004-2008 Novell. BSD licensed.

Success

➜  my-app git:(master) ✗ pvk2pfx -pvk my_win_private_key.pvk -spc my_win_certificate.cer -pfx my_signing_key.pfx -po my_password</pre>
<blockquote>refer: [link](https://www.npmjs.com/package/electron-installer-windows#signing)</blockquote>
默认Mac下安装mono后，应该会有<code>makecert</code>和<code>pvk2pfx</code>这两个工具。如果没有，可以到Win下进行签名，或者直接从网上下载。
<h3>有关自动更新</h3>
electron-builder的自动更新机制依赖的是Squirrel.Mac和Squirrel.windows，并且最新版本已经可以实现不需要准备一台接口服务器，只需要静态服务器便可实现。
<blockquote>Refer: [link](https://github.com/electron-userland/electron-builder/blob/master/docs/Auto%20Update.md)</blockquote>
一般，用S3是比较省心的，按照文档配置就可以实现Win和Mac双平台的自动更新，只需要代码响应相应事件即可。

但我们所使用的是GitHub Private Repo，有鉴权的问题。采用的解决方案是Nuts + GitHub， Nuts作为更新接口，GitHub提供存储。

相关配置如下：
<pre class="lang:js decode:true">    "publish": [
      {
        "provider": "generic",
        "url": "http://down.ijser.cn/download"
      },
      {
        "provider": "github",
        "owner": "ijse",
        "repo": "desktop-app"
      }
    ],
</pre>
自动静默后台更新实现：
<pre class="lang:js decode:true ">const log = global.logger
const os = require('os')
const { app, dialog } = require('electron')

const autoUpdater = require('electron-updater').autoUpdater

autoUpdater.logger = log
autoUpdater.logger.transports.file.level = 'info'

module.exports = function () {
  try {
    autoUpdater.checkForUpdates()
    autoUpdater.on('update-downloaded', () =&gt; {
      const ret = dialog.showMessageBox(app.$mainWindow, {
        type: 'question',
        buttons: [ '重启并使用新版本', '下次打开时升级' ],
        defaultId: 0,
        cancelId: 1,
        title: '新版本已经准备好了',
        message: '马上重启并使用新版本吗？'
      })
      if (ret === 0) {
        autoUpdater.quitAndInstall()
      }
    })
  } catch (e) {
    log.error(e)
  }
}
</pre>
<h3>调试打包过程</h3>
electron-builder遵循惯用的调试日志方式：
<pre class="lang:default decode:true ">NODE_ENV=electron-builder build</pre>
这样执行打包过程后，会输出详细的过程日志，对调试很有帮助。
<h3>最后</h3>
electron-builder 因为文档有点不太完美，实际学习起来会有点吃力，遇到文档不能很好解决的问题，可以去查Issues 和 代码，另外，关注Releases最新版本和发版说明。