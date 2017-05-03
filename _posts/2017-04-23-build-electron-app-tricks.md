---
ID: 177
post_title: 设计称手的 Electron 程序技巧
author: ijse
post_date: 2017-04-23 11:42:18
post_excerpt: ""
layout: post
permalink: >
  https://ijser.cn/build-electron-app-tricks/
published: true
---
Electron 客户端程序在开发时不同于一般网页开发，程序的生命周期也是不一样的，一般需要经历开发、测试、打包构建、分发程序、收集反馈等。

这里介绍一些技巧， 可以方便在开发、测试及发布后更好地调试。

<!--more-->
<h2>留意系统平台差异</h2>
用Electron来开发桌面应用程序最大的特点之一是跨平台性，主流的三种平台：Mac, Windows和Linux之间有一些不同，比如：
<ol>
 	<li>Windows下的路径是类似<code>C:\Windows\xxx, 而Mac和Linux下是/var/www。在处理时最好使用path.resolve()处理一下。</code></li>
 	<li>键盘键位的不同，比如Mac下的特有的<code>Command按键</code></li>
 	<li>窗口管理的不同，比如Windows的窗口控制按钮是在右上角</li>
</ol>
<h2>保持依赖更新</h2>
至少保持electron-builder更新，它会修复很多问题，诸如自动更新、软件包安装、以及平台运行等一些问题。
<h2>阻止ErrorDialog显示</h2>
在程序出现Bug情况下，用户可能会收到错误提示框，影响用户体验，可能我们希望隐藏提示框，由程序统一做一些处理。

Electron没有提供明确的mute方式，但是我们可以通过覆盖方法方式：
<pre class="lang:js decode:true">import { dialog } from 'electron'

// Disable error dialogs show
dialog._showErrorBox = dialog.showErrorBox
dialog.showErrorBox = function (title, content) {
  global.logger(title + '\n' + content)
}</pre>
<h2>日志记录上报</h2>
在用户运行程序时出现的各种问题，最好的方式是可以记录到文件，然后通过其它方式上报分析，如用户主动发送、或者自动上传。

推荐一款比较好用的插件[electron-log](https://github.com/megahertz/electron-log)。默认的日志记录位置是：
<ul>
 	<li><strong>on Linux:</strong> <code>~/.config/&lt;app name&gt;/log.log</code></li>
 	<li><strong>on OS X:</strong> <code>~/Library/Logs/&lt;app name&gt;/log.log</code></li>
 	<li><strong>on Windows:</strong> <code>%USERPROFILE%\AppData\Roaming\&lt;app name&gt;\log.log</code></li>
</ul>
程序可以自动读取上报。

另外，我们也可以同步页面(renderer process)中的console日志：
<pre class="lang:js decode:true ">// sync logs
const logger = remote.require('electron-log')
window.logger = logger

const proxyLog = (target, dest) =&gt; {
  const original = console[target]
  console[target] = (...args) =&gt; {
    logger[dest](...args)
    original(...args)
  }
}

proxyLog('log', 'info')
proxyLog('info', 'info')
proxyLog('warn', 'warn')
proxyLog('error', 'error')

</pre>
这样便可以统一日志输出，方便搜集。
<h2>埋入调试快捷键</h2>
通常情况下，发布的程序包在用户端是不提供调试功能的，一般我们不希望用户打开devtool。但为了方便调试，我们可以默默埋入一个调试快捷键，在需要的时候调出devtool进行页面调试。

我们可以使用Electron提供的[Accelerator](https://electron.atom.io/docs/api/accelerator/)，设置一个较复杂，用户不容易按的组合快捷键：
<pre class="lang:js decode:true ">import { app globalShortcut } from 'electron'

app.on('ready', () =&gt; {
  globalShortcut.register('CmdOrCtrl+Alt+Shift+F11', () =&gt; {
    mainWindow.webContents.openDevTools()
  })
})</pre>
另外一种添加更复杂快捷键的方式是通过[Mousetrap](https://github.com/ccampbell/mousetrap) 这个模块。
<pre class="lang:js decode:true ">// bind keys
import Mousetrap from 'mousetrap'
import {
  remote,
  ipcRenderer as ipc
} from 'electron'

// open devtools in production for debug
Mousetrap.bind('d e b u g enter', () =&gt; {
  ipc.send('open-devtools')
})

</pre>
在main中接收ipc消息，打开devtool即可。
<h2>找回复制粘贴功能</h2>
默认情况下，在不开启Edit菜单的情况下，页面中是不能够复杂粘贴的，网上很多方法都是添加回Edit菜单。但也可以不通过这种方式：

推荐两个插件：
<ul>
 	<li><a href="https://github.com/parro-it/electron-input-menu">electron-input-menu</a></li>
 	<li><a href="https://github.com/parro-it/electron-contextmenu-middleware">electron-contextmenu-middleware</a></li>
</ul>
用法也很简单，在renderer中执行：
<pre class="lang:js decode:true">// support copy, paste, cut and etc.
// with shortcut and context menu
const inputMenu = require('electron-input-menu')
const context = require('electron-contextmenu-middleware')
inputMenu.registerShortcuts();

context.use(inputMenu)
context.activate()

</pre>
<h2>有关打包和自动更新</h2>
见另一篇博文：https://ijser.cn/about-electron-builder/
<h2>获取用户设备唯一ID</h2>
推荐插件：[node-machine-id](https://github.com/automation-stack/node-machine-id)

用法很简单：
<pre class="lang:js decode:true ">import {machineId, machineIdSync} from 'node-machine-id';

// Asyncronous call with async/await or Promise

async function getMachineId() {
    let id = await machineId();
    ...
}

machineId().then((id) =&gt; {
    ...
})

// Syncronous call

let id = machineIdSync()
// id = c24b0fe51856497eebb6a2bfcd120247aac0d6334d670bb92e09a00ce8169365
let id = machineIdSync({original: true})
// id = 98912984-c4e9-5ceb-8000-03882a0485e4</pre>
在程序第一次启动时获取，然后把它缓存起来。
<h2>保存用户配置</h2>
推荐插件：[electorn-config](https://github.com/sindresorhus/electron-config)

配置会以文件形式保存在程序目录中，可以通过<code>app.getPath('userData')获得目录路径。</code>

&nbsp;