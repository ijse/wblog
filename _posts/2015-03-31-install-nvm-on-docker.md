---
ID: 60
post_title: >
  Docker 笔记 打造node.js开发环境
  安装nvm
author: ijse
post_date: 2015-03-31 12:42:33
post_excerpt: ""
layout: post
permalink: https://ijser.cn/install-nvm-on-docker/
published: true
---
通过制作一个Docker镜像，统一管理开发环境，使不同开发者可以在任何平台下开发，同时拥有相同的开发环境配置。

文章所用的示例代码发布在Github上了，<a href="https://github.com/ijse/nodejs-docker-image">点击此处查看</a>

<!--more-->
<h4 id="toc_0">创建Dockerfile文件</h4>
<pre><code class="language-dockerfile">
###########
# Usage:
#
#   docker run -it -v &lt;project-resource-directory&gt;:/workspace \
#                  --name &lt;container-name&gt; [image-name]
#

FROM centos
MAINTAINER ijse

RUN yum -y update
RUN yum -y install tar git

# 配置环境变量
ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 0.10.32
ENV WORK_DIR /workspace

# 下载和配置Node.js环境
# 这些命令一定要写在一起, 否则`nvm`命令会找不到
RUN curl https://raw.githubusercontent.com/creationix/nvm/v0.24.0/install.sh | bash \
    &amp;&amp; source $NVM_DIR/nvm.sh \
    &amp;&amp; nvm install v$NODE_VERSION \
    &amp;&amp; nvm use v$NODE_VERSION \
    &amp;&amp; nvm alias default v$NODE_VERSION

ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH      $NVM_DIR/v$NODE_VERSION/bin:$PATH

# 设置工作目录
WORKDIR $WORK_DIR

# 公开镜像的80端口
EXPOSE 80

CMD node
</code></pre>
<ul>
 	<li>每条<code>RUN</code>命令即是一层（Layer），Docker会将其缓存，对提高存储加快build速度很有用。</li>
 	<li>每次<code>RUN</code>命令，都被分配了一个单独的进程环境执行，因此一些环境变量等是不同的。</li>
 	<li><code>CMD</code>命令相当于<code>docker run</code>中执行的命令，</li>
</ul>
<h4 id="toc_1">构建出image</h4>
<pre><code>$ docker build --force-rm -t ijse/nvm .
$ docker images
</code></pre>
构建成功后，会看到出现了一个<em>ijse/nvm</em>的镜像。

注意，build 的时候到如下一步时：
<pre><code>=&gt; Close and reopen your terminal to start using nvm
</code></pre>
不要动，这时候正在下载安装Node.js，可能会花费比较长时间，如果等得不耐烦，可以另开一个终端，用`docker ps`查到容器Id, 然后`docker attach &lt;容器Id&gt;`来查看进度。
<h4>试着跑一下~</h4>
镜像构建成功后，就应该出现在`docker images`列表中了，用如下命令试着运行一下容器内的终端：
<pre><code>$ docker run -it --name test-nvm ijse/nvm
</code></pre>
接着便进入了node的交互命令行下，可以执行<code>process.version</code>查看容器中node.js的版本。
<h4 id="toc_2">把代码装载</h4>
由于代码会经常更新，将它们打包进镜像中不太合适，一般通过目录挂载的方式，将代码目录放在宿主机上，这样可以分开管理。

启动的时候添加<code>-v</code>参数挂载代码目录：
<pre><code>$ docker run -it --name test-nvm -p 80 -v /myPorject:/workspace ijse/nvm bash
</code></pre>
运行后即进入容器的bash下，此时可以继续执行开发相关的命令了，如<code>npm install &amp;&amp; npm start</code>。

由<code>-p 3000:80</code>参数，将容器的80端口绑定到宿主机的3000端口上，于是我们可以访问<code>http://localhost:3000</code>来访问容器的80端口服务了。
<blockquote><strong>注意：</strong>
<ul>
 	<li>如果是在Windows或Mac系统下，由于Docker是运行在虚拟机里的，所以访问时<code>localhost</code>要换为虚拟机的ip地址。</li>
 	<li><code>npm install</code>的时候，由于挂载机制，在Windows下可能需要添加<code>--no-bin-links</code>参数</li>
</ul>
</blockquote>
<h4 id="toc_3">全部代码</h4>
示例的代码放到了Github上： <a href="https://github.com/ijse/nodejs-docker-image">http://github.com/ijse/nodejs-docker-image</a>
<h4 id="toc_4">后记</h4>
这个示例很简单，而通常情况下我们的项目会更复杂些，不仅环境会有很多配置修改，还会需要其它服务，如MongoDB, Redis，甚至其它一些Web Services等。我们可以把这些服务都打包进一个镜像中，也可以分开。使用差不多的方式编写相应的Dockerfile，并配置环境即可。

于是便可以看出Docker的方便之处：
<ol>
 	<li>直接分发Dockerfile即可，其它开发者可以自己构建出镜像，并且与大家开发环境一致</li>
 	<li>跨平台开发的方便，无论宿主机是什么平台，Docker都可以保证代码运行的环境与线上环境一致</li>
 	<li>维护方便，只需要维护Dockerfile即可随时管理更新开发环境</li>
</ol>
Docker上手很快，一般使用的话，运行几条命令即可满足需求。简直是程序开发的福音。