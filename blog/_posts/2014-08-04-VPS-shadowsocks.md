---
title: 'VPS上架设shadowsocks服务器'
layout: post
categories:
    - Tutorial
tags:
    - [Tutorial,Proxy]
---

<p>
	哈哈，今天买了一个国外的VPS，架设了shadowsocks，以后再也不怕上google scholar啦~（你懂的）。
</p>
<p>
	GoAgent也快挂了，一直在寻找可用IP，经常失效，特别麻烦，所以才有了买VPS的想法。
</p>
<p>
	网上一搜，正好搬瓦工(BandwagonHost)优惠还在继续，最低配的VPS只要3.99美元一个月，每月100G流量，入手~
</p>
<p>
	于是开始钻研咋布置shadowsocks，网上看了几篇教程，发现还是蛮容易的，下面是具体过程。
</p>
<p>
	<br />
</p>
<p>
	<strong>搬瓦工VPS准备工作：</strong>
</p>
<p>
	1.进入搬瓦工的控制面板，先在Main controls里面把VPS关掉stop，再点击install new OS，选择Debian 6.0 x86 minimal，使用Debian节省空间内存~
</p>
<p>
	2.这时会告诉你VPS的SSH Port和Root密码，记录下来。等一会儿自动安装系统完毕我们就进入主机开始操作了，windows下推荐使用PuTTY。如果不熟练的可以下载那个installer。
</p>
<p>
	3.打开PuTTY，IP address填写VPS的IP地址，Port填写刚才记录下来的端口，最好把这个会话保存下来，在Saved Sessions下面随便自己起个名字，例如B-VPS，点击旁边的Save。
</p>
<p>
	4.双击B-VPS，login as输入root，password填写之前记录的Root密码（输入过程屏幕不会有变化，输入完毕直接回车。
</p>
<p>
	5.先改密码，之前那个基本乱码级别记不住。输入passwd root，再输入两次新密码，以后就可以用自己设置的密码登陆了。
</p>
<p>
	<br />
</p>
<p>
	<strong>Shadowsocks部署工作：</strong>
</p>
<p>
	1.这里我们部署的是shadowsocks-libev，资源占用小，部署简单。
</p>
<p>
	2.先追加软件源<pre class="prettyprint linenums">echo "deb http://shadowsocks.org/debian squeeze main" &gt;&gt; /etc/apt/sources.list</pre>这个是debian6的shadowsocks源地址
</p>
<p>
	3.再更新软件源并安装shadowsocks<pre class="prettyprint linenums">apt-get update apt-get install shadowsocks</pre>
</p>
<p>
	4.改写默认shadowsocks配置文件<pre class="prettyprint linenums">vi /etc/shadowsocks/config.json</pre>输入i改写，改后先按Esc再输入:wq回车保存退出。
</p>
<pre class="prettyprint linenums">
{
&nbsp;&nbsp;&nbsp;&nbsp; "server":"VPS的ip",
&nbsp;&nbsp;&nbsp;&nbsp; "server_port":8388,
&nbsp;&nbsp;&nbsp;&nbsp; "local_port":1080,
&nbsp;&nbsp;&nbsp;&nbsp; "password":"balabala", #认证密码
&nbsp;&nbsp;&nbsp;&nbsp; "timeout":60,
&nbsp;&nbsp;&nbsp;&nbsp; "method":"table" #加密方式，默认table，推荐aes-256-cfb
}
</pre>
<p>
	5.如果使用了table以外的加密方式，请安装M2Crypto配合。<pre class="prettyprint linenums">apt-get install python-m2crypto</pre>
</p>
<p>
	6.最后重启shadowsocks服务即可大功告成！<pre class="prettyprint linenums">/etc/init.d/shadowsocks stop
/etc/init.d/shadowsocks start</pre>
</p>
<p>
	<br />
</p>
<p>
	<strong>Shadowsocks客户端使用：</strong>
</p>
<p>
	1.这个可是全平台适用，Windows、Mac、Android、iOS通吃。
</p>
<p>
	2.Windows和Mac端可以使用shadowsocks-gui，Android端使用影梭（shadowsocks），iOS端越狱的使用bigboss源的ShadowSocks，未越狱的使用AppStore的shadowsocks（有一定限制）。
</p>
<p>
	3.具体使用方法不赘述，配置参数见前面自己在VPS设置的shadowsocks配置文件。
</p>
<br />
<p>{{ page.date | date_to_string }}</p>