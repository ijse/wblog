---
ID: 136
post_title: >
  NPM管理two
  package.json项目的version同步更新
author: ijse
post_date: 2017-03-27 14:49:16
post_excerpt: ""
layout: post
permalink: >
  https://ijser.cn/npm-two-package-json-version-sync/
published: true
---
最近在重构一个Electron项目，使用了 [two package.json structure](https://github.com/electron-userland/electron-builder/wiki/Two-package.json-Structure)，但怎样方便地同步更新两个<code>version</code>

<!--more-->

项目结构类似如下：
<pre class="lang:default highlight:0 decode:true" title="two package.json structure">- project/
  - app/
    - package.json
  - package.json</pre>
打包时使用的是<code>project/app/package.json</code>中的<code>version</code>，但习惯上一般是在<code>project/</code>目录下执行<code>npm version</code>。

默认执行<code>npm version</code>更新版本号时，只会自动更新当前目录下<code>package.json</code>中的<code>version</code>字段，<code>project/app/</code>下的只能手动更新。

最终的解决方式如下：
<pre class="lang:js decode:true">// project/package.json
"scripts": {
    "version": "pushd app; npm version $npm_package_version; git add -A; popd"
}
</pre>
参考文档：[https://docs.npmjs.com/cli/version](https://docs.npmjs.com/cli/version)