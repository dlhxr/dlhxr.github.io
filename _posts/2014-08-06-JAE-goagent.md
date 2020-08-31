---
title: 'JAE架设国内反向GoAgent'
layout: post
categories:
    - Tutorial
tags:
    - [Tutorial,Proxy]
---

<p>
	本文写得很简陋，只给出一个大体过程，细节遇到问题可以百度得到答案。
</p>
<p>
	GoAgent应该被很多人熟知，都使用这个出去看看，不过这次要说的是反向GoAgent。
</p>
<p>
	顾名思义，反向GoAgent就是国外代理回大陆，你懂的，国外很多大陆的服务有IP检测，非大陆IP不提供服务。
</p>
<p>
	此外呢，某些国内的网络访问一些国内的网络速度慢，或者无法访问，此种情况多出现在教育网上~
</p>
<p>
	闲话不多说，下面就是部署GoAgent PHP到<a target="_blank" href="http://jae.jd.com/">JAE（京东云擎）</a>的过程
</p>
<p>
	首先注册京东云擎，需要手机验证，创建一个php应用，得到该应用的Git地址
</p>
<p>
	使用Git克隆下来<pre class="prettyprint linenums">git clone https://code.jd.com/dlhxr/jae_XXXX.git</pre>删除所有克隆下来的文件，将GoAgent文件夹中server下的php里面所有文件复制过来
</p>
<p>
	提交更改，push到代码库<pre class="prettyprint linenums">
	git add --all
	git commit -m "init"
	git push origin master</pre>
</p>
<p>
	进入云擎管理界面，点击快速部署，让应用使用最新的代码
</p>
<p>
	本地GoAgent的配置文件把php下面的enable=1，然后填入创建的php应用地址即可，如果不用gae可以让gae的enable=0
</p>
<p>
	本地代理设置，默认端口是8088
</p>

<p>{{ page.date | date_to_string }}</p>