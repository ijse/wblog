---
ID: 57
post_title: package.json for NPM 文件详解
author: ijse
post_date: 2015-03-27 18:35:15
post_excerpt: ""
layout: post
permalink: >
  https://ijser.cn/npm-package-json-document/
published: true
---
<code>package.json</code>文件描述了一个NPM包的所有相关信息，包括作者、简介、包依赖、构建等信息。格式必须是严格的JSON格式。

通常我们在创建一个NPM程序时，可以使用<code>npm init</code>命令，通过交互式的命令，自动生成一个<code>package.json</code>文件，里面包含了常用的一些字段信息，但远不止这么简单。通过完善<code>package.json</code>文件，我们可以让<code>npm</code>命令更好地为我们服务。

<h5 id="toc_0">name</h5>

<code>name</code>和<code>version</code>是package.json中最重要的两个字段，也是发布到NPM平台上的唯一标识，如果没有正确设置这两个字段，包就不能发布和被下载。

下面是官方给出的一些建议:

<ul>
<li>名字里不要再包含&quot;js&quot;和&quot;node&quot;，因为默认NPM包就是node.js程序，不过你可以通过<code>engines</code>字段来指定。</li>
<li>名字将会被作为url的一部分，所有要符合http url的一般命名规则，不能包含url非法字符，也不能以<code>.</code>和<code>_</code>开头。</li>
<li>名字也将作为<code>require()</code>命令的参数，所以应该尽量简明。</li>
<li>如果包要发布到NPM平台上的话，最好先检查下有没有重名, 并且字母只能全部小写。</li>
</ul>

新版本的NPM可以指定scope, 名字可以加前缀标识，如<code>@ijse/mypackage</code>。

<h5 id="toc_1">version</h5>

这个字段的取值需要符合<a href="https://github.com/npm/node-semver">node-semver</a>的规则，详细可以见其文档。

<h5 id="toc_2">description</h5>

包的描述信息，将会在<code>npm search</code>的返回结果中显示，以帮助用户选择合适的包。

<h5 id="toc_3">keywords</h5>

包的关键词信息，是一个字符串数组，同上也将显示在<code>npm search</code>的结果中。

<h5 id="toc_4">homepage</h5>

包的主页地址

<h5 id="toc_5">bugs</h5>

包的bug跟踪主页地址，应该如下设置：

<pre><code class="language-json">bugs: {
  &quot;url&quot;: &quot;http://github.com/ijse/project/issues&quot;,
  &quot;i@ijser.cn&quot;: &quot;my@ijser.cn&quot;
}
</code></pre>

<h5 id="toc_6">license</h5>

包的开源协议名称

<h5 id="toc_7">author</h5>

包的作者，可以是字符串或对象：

<pre><code class="language-json">author: {
  &quot;name&quot;: &quot;ijse&quot;,
  &quot;i@ijser.cn&quot;: &quot;my@ijse.cn&quot;,
  &quot;url&quot;: &quot;http://www.ijser.cn&quot;
}
</code></pre>

或者:

<pre><code class="language-json">author: &quot;ijse &lt;my@ijser.cn&gt; (http://www.ijser.cn)&quot;
</code></pre>

<h5 id="toc_8">contributors, maintainers</h5>

包的贡献者，是一个数组。

<h5 id="toc_9">files</h5>

包所包含的所有文件，可以取值为文件夹。

通常我们还是用<code>.npmignore</code>来去除不想包含到包里的文件。

<h5 id="toc_10">main</h5>

包的入口文件，如<code>index.js</code>

<h5 id="toc_11">bin</h5>

如果你的包里包含可执行文件，通过设置这个字段可以将它们包含到系统的<code>PATH</code>中，这样直接就可以运行，很方便。如：

<pre><code class="language-json">&quot;bin&quot;: {
  &quot;iapp&quot;: &quot;./cli.js&quot;
}
</code></pre>

当包被安装后，NPM将创建一个<code>cli.js</code>文件的链接到<code>/usr/local/bin/iapp</code>下。

<h5 id="toc_12">man</h5>

为系统的<code>man</code>命令提供帮助文档, 如：

<pre><code class="language-json">&quot;man&quot;: &quot;./man/doc.1&quot;
</code></pre>

帮助文件的文件名必须以<code>数字</code>结尾，如果是压缩的，需要以<code>.gz</code>结尾。

如果是字符串数组：

<pre><code class="language-json">&quot;name&quot;: &quot;foo&quot;,
&quot;man&quot;: [&quot;./man/foo.1&quot;, &quot;./man/bar.1&quot;, &quot;./man/foo.2&quot; ]
</code></pre>

则分别可以<code>man foo</code>, <code>man foo-bar</code>, <code>man 2 foo</code>来查看。

<h5 id="toc_13">directories</h5>

CommonJS包所要求的目录结构信息，目前除了告诉别人你的程序目录结构，貌似没有别的什么用。
下级字段可以是：lib, bin, man, doc, example。
每个都是字符串

<h5 id="toc_14">repository</h5>

包的仓库地址。如：

<pre><code class="language-json">&quot;repository&quot;: {
  &quot;type&quot;: &quot;git&quot;,
  &quot;url&quot;: &quot;http://github.com/ijse/project.git&quot;
}
</code></pre>

<h5 id="toc_15">scripts</h5>

通过设置这个可以使NPM调用一些命令脚本，封装一些功能。

<h5 id="toc_16">config</h5>

添加一些设置，可以供<code>scripts</code>读取用，同时这里的值也会被添加到系统的环境变量中。

<pre><code class="language-json">&quot;name&quot;: &quot;foo&quot;,
&quot;config&quot;: {
  &quot;port&quot;: &quot;8080&quot;
}
</code></pre>

<code>npm start</code>的时候会读取到<code>npm_package_config_port</code>环境变量。

同时也可以使用<code>npm config</code>命令来修改设置：

<pre><code>npm config set foo:port 8001
</code></pre>

<h5 id="toc_17">dependencies</h5>

指定依赖的其它包，这些依赖是指包发布后正常执行时所需要的，如果是开发中依赖的包，可以在<code>devDependencies</code>设置。

通常使用下面命令来安装：

<pre><code>npm install --save otherpackage
</code></pre>

形式可以有如下多种：

<ul>
<li><code>version</code> 严格匹配某个版本</li>
<li><code>&gt;version</code> 必须大于某个版本</li>
<li><code>&gt;=version</code></li>
<li><code>&lt;version</code></li>
<li><code>&lt;=version</code></li>
<li><code>~version</code> 大概匹配某个版本</li>
<li><code>^version</code> 兼容某个版本</li>
<li><code>1.2.x</code> 可以是1.2.0, 1.2.1等等，但不能是1.3.0</li>
<li><code>http://...</code> 指定tarball的url地址</li>
<li><code>*</code> 任何版本都可以</li>
<li><code>&quot;&quot;</code> 同上</li>
<li><code>version1 - version2</code> &gt;=version1 &lt;=version2</li>
<li><code>range1 || range2</code> 满足range1 或range2</li>
<li><code>git://...</code> git地址</li>
<li><code>user/repo</code> 同上</li>
<li><code>tag</code> 指定某个tag的版本</li>
<li><code>path/path</code> 本地包所有文件夹</li>
</ul>

下面都是可以用的：

<pre><code class="language-json">{ &quot;dependencies&quot; :
  { &quot;foo&quot; : &quot;1.0.0 - 2.9999.9999&quot;
  , &quot;bar&quot; : &quot;&gt;=1.0.2 &lt;2.1.2&quot;
  , &quot;baz&quot; : &quot;&gt;1.0.2 &lt;=2.3.4&quot;
  , &quot;boo&quot; : &quot;2.0.1&quot;
  , &quot;qux&quot; : &quot;&lt;1.0.0 || &gt;=2.3.1 &lt;2.4.5 || &gt;=2.5.2 &lt;3.0.0&quot;
  , &quot;asd&quot; : &quot;http://asdf.com/asdf.tar.gz&quot;
  , &quot;til&quot; : &quot;~1.2&quot;
  , &quot;elf&quot; : &quot;~1.2.3&quot;
  , &quot;two&quot; : &quot;2.x&quot;
  , &quot;thr&quot; : &quot;3.3.x&quot;
  , &quot;lat&quot; : &quot;latest&quot;
  , &quot;dyl&quot; : &quot;file:../dyl&quot;
  }
}
</code></pre>

Git URL可以有如下种形式：

<pre><code>git://github.com/user/project.git#commit-ish
git+ssh://user@hostname:project.git#commit-ish
git+ssh://user@hostname/project.git#commit-ish
git+http://user@hostname/project/blah.git#commit-ish
git+https://user@hostname/project/blah.git#commit-ish
</code></pre>

<h5 id="toc_18">devDependencies</h5>

这些依赖只有在开发时候才需要。

<pre><code>npm install --save-dev mypack
</code></pre>

<h5 id="toc_19">peerDependencies</h5>

相关的依赖，如果你的包是插件，而用户在使用你的包时候，通常也会需要这些依赖（插件），那么可以将依赖列到这里。

举个例子，如<code>karma</code>, 它的<code>package.json</code>中有设置：

<pre><code class="language-json">&quot;peerDependencies&quot;: {
  &quot;karma-jasmine&quot;: &quot;~0.1.0&quot;,
  &quot;karma-requirejs&quot;: &quot;~0.2.0&quot;,
  &quot;karma-coffee-preprocessor&quot;: &quot;~0.1.0&quot;,
  &quot;karma-html2js-preprocessor&quot;: &quot;~0.1.0&quot;,
  &quot;karma-chrome-launcher&quot;: &quot;~0.1.0&quot;,
  &quot;karma-firefox-launcher&quot;: &quot;~0.1.0&quot;,
  &quot;karma-phantomjs-launcher&quot;: &quot;~0.1.0&quot;,
  &quot;karma-script-launcher&quot;: &quot;~0.1.0&quot;
}
</code></pre>

这些都是<code>karma</code>的相关插件，一般使用<code>karma</code>的时候都会需要。

<h5 id="toc_20">bundledDependencies</h5>

绑定的依赖包，发布的时候这些绑定包也会被一同发布。

<h5 id="toc_21">optionalDependencies</h5>

即使这些依赖没有，也可以正常安装使用

<h5 id="toc_22">engines</h5>

指定包运行的环境

<pre><code class="language-json">&quot;engines&quot;: {
  &quot;node&quot;: &quot;&gt;=0.10.3 &lt; 0.12&quot;,
  &quot;npm&quot;: &quot;~1.0.20&quot;
}
</code></pre>

<h5 id="toc_23">engineStrict</h5>

设置为<code>true</code>强制限定 <code>engine</code>

<h5 id="toc_24">os</h5>

指定你的包可以在哪些系统平台下运行。

<pre><code class="language-json">&quot;os&quot;: [ &quot;darwin&quot;, &quot;linux&quot;, &quot;!win32&quot; ]
</code></pre>

即可以在<code>darwin</code>和<code>linux</code>平台下运行，而不能在<code>win32</code>下。这里设定的取值是来自<code>process.platform</code>的。

<h5 id="toc_25">cpu</h5>

可以指定包运行的cpu架构，如

<pre><code class="language-json">&quot;cpu&quot;: [ &quot;x64&quot;, &quot;!arm&quot; ]
</code></pre>

取值来自<code>process.arch</code>。

<h5 id="toc_26">preferGlobal</h5>

如果你的包是命令行运行的，那可以将其设置为<code>true</code>建议用户全局(<code>npm install -g</code>)安装。但它并不强制用户。

<h5 id="toc_27">private</h5>

设为<code>true</code>这个包将不会发布到NPM平台下。

<h5 id="toc_28">publishConfig</h5>

这个字段用于设置发布时候的一些设定。尤其方便你希望发布前设定指定的<code>tag</code>或<code>registry</code>。

也可以设定其它子字段，但只有<code>tag</code>和<code>registry</code>会影响到发布。

<h3 id="toc_29">NPM的一些默认值说明</h3>

<ul>
<li><p><code>&quot;scripts&quot;: { &quot;start&quot;: &quot;node server.js&quot; }</code><br/>
如果在项目根目录下含有<code>server.js</code>文件，则NPM会自动设置此值。</p></li>
<li><p><code>&quot;scripts&quot;: { &quot;preinstall&quot;: &quot;node-gyp rebuild&quot; }</code><br/>
如果在项目根目录下含有<code>binding.gyp</code>文件，则NPM会自动设置此值。</p></li>
<li><p><code>&quot;contributors&quot;: [...]</code><br/>
如果项目根目录下含有<code>AUTHORS</code>文件，则NPM会自动将每一行以<code>Name &lt;i@ijser.cn&gt; (url)</code>的格式读取并设定此字段。</p></li>
</ul>