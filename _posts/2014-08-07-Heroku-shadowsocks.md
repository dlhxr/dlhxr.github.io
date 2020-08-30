---
title: '在国外老牌云空间Heroku架设Shadowsocks'
layout: post
categories:
    - Tutorial
tags:
    - [Tutorial,Proxy]
---

<p>
	最近搞代理搞上瘾了，晚上突然发现一种免费部署shadowsocks的方案，于是进行尝试~
</p>
<p>
	国外老牌云空间<a target="_blank" href="https://www.heroku.com/">Heroku</a>免费不限流量，而且也有改好支持Heroku的shadowsocks版本，开始部署吧~
</p>
<p>
	（项目名称是<a target="_blank" href="https://github.com/mrluanma/shadowsocks-heroku">shadowsocks-heroku</a>，使用&nbsp;WebSockets代替raw sockets，使之可以运行在云空间上。）
</p>
<p>
	<br />
</p>
<p>
	<strong>账号注册及初始化：</strong>
</p>
<p>
	1.进入<pre class="prettyprint linenums">https://www.heroku.com/</pre>点击sign up for free，需要提供邮箱地址进行验证（不需要其他信息哦~），值得一提的是国内邮箱连163都被block了，所以最后我还是用gmail申请的。
</p>
<p>
	2.登陆你的邮箱，点击链接激活自己的用户。
</p>
<p>
	3.安装Heroku专用工具Heroku Toolbelt，下载地址<pre class="prettyprint linenums">https://toolbelt.heroku.com/</pre>有的时候可能访问不了，那就自己百度吧~
</p>
<p>
	4.安装Toolbelt过程中，如果之前安装过git的话把安装页面的git选项去掉，推荐使用git官方的工具。
</p>
<p>
	5.安装完毕后，进入cmd分别输入heroku和git，如果有参数提示，那么就安装成功了~
</p>
<p>
	<br />
</p>
<p>
	<strong>部署shadowsocks-heroku：</strong>
</p>
<p>
	1.首先找到shadowsocks-heroku项目，地址是<pre class="prettyprint linenums">https://github.com/mrluanma/shadowsocks-heroku</pre>可以使用<pre class="prettyprint linenums">git clone https://github.com/mrluanma/shadowsocks-heroku.git</pre>或者直接下载代码的zip包。
</p>
<p>
	2.进入cmd，输入heroku login，输入用户名和密码，如果没有ssh key先根据提示创建key，会自动上传到服务器。
</p>
<p>
	3.创建一个新的app，输入heroku create，新创建的app名称是一个类似于still-tor-8707这样的名字，看heroku create的返回信息。
</p>
<p>
	4.设立一个git库，用于上传shadowsocks，直接把新建的app的git库给clone下来。<pre class="prettyprint linenums">git clone git@heroku.com:still-tor-8707.git</pre>
</p>
<p>
	5.把之前从github下载的shadowsocks里面所有文件放进still-tor-8707文件夹下面，输入
<pre class="prettyprint linenums">
git add --all
git commit -m "init"
git push origin master
</pre>
</p>
<p>
	6.应用会自动部署，会看到一些提示~
</p>
<p>
	7.更改config，主要是加密方式和密码<pre class="prettyprint linenums">heroku config:set METHOD=rc4 KEY=foobar</pre>其中foobar是密码，可以自己设置。这时候服务端就部署好了~
</p>
<p>
	<br />
</p>
<p>
	<strong>客户端shadowsocks-heroku：</strong>
</p>
<p>
	1.官方的shadowsocks-gui是用不了的，因为使用了WebSockets，会有一定的问题（如果想尝试远程端口是80）。
</p>
<p>
	2.所以我们就要自己做客户端了，到NodeJs官网上面下载node的binary文件（Windows和Mac分别下载对应的文件），帮助我们建立本地服务器。
</p>
<p>
	3.这里以windows为例，系统是64位的就使用64位的binary，32位就用32位的binary。
</p>
<p>
	4.把之前下载的shadowsocks-heroku源码中的args.js、config.json、encrypt.js、local.js、merge_sort.js放入和binary一个目录下面。
</p>
<p>
	5.打开cmd，进入存放刚才几个文件的目录，输入<pre class="prettyprint linenums">node local.js -s still-tor-8707.herokuapp.com -l 1080 -m rc4 -k foobar</pre>其中still-tor-8707是应用名称，1080是本地端口号，rc4是加密方法，foobar是shadowsocks的密码，根据自己的情况修改~
</p>
<p>
	6.于是就可以使用127.0.0.1:1080代理咯~
</p>
<p>
	7.如果觉得不方便，可以自己建一个bat文件
<pre class="prettyprint linenums">
@echo off
path = %~dp0node_shadowsocks.exe
start "" /b "%path%" local.js -s still-tor-8707.herokuapp.com -l 1080 -m rc4 -k foobar
</pre>
</p>
<p>
	8.如果想自动后台，可以使用下面的代码
<pre class="prettyprint linenums">
@echo off
if "%1" == "h" goto Begin
mshta vbscript:createobject("wscript.shell").run("%~nx0 h",0)(window.close)&amp;&amp;exit
:Begin
path = %~dp0node_shadowsocks.exe
start "" /b "%path%" local.js -s still-tor-8707.herokuapp.com -l 1080 -m rc4 -k foobar
exit
</pre>
</p>
<p>
	8.不过免费的服务的确不太给力，勉强能用啦~
</p>
<br>
<p>{{ page.date | date_to_string }}</p>