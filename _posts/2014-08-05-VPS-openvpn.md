---
title: 'VPS上架设OpenVPN服务器'
layout: post
categories:
    - Tutorial
tags:
    - [Tutorial,Proxy]
---

<p>
	忽然发现iOS未越狱的shadowsocks限制还是蛮大的，所以今天又在同一个VPS上面架设了OpenVPN。
</p>
<p>
	继续上个帖子~
</p>
<p>
	<strong>搬瓦工VPS准备工作：</strong>
</p>
见上一篇日志。
<p>
	<br />
</p>
<p>
	<strong>OpenVPN部署工作：</strong>
</p>
<p>
	1.这里我们先要测试自己的VPS是否支持架设OpenVPN
<pre class="prettyprint linenums">cat /dev/net/tun</pre>如果结果为：cat: /dev/net/tun: File descriptor in bad state 说明tun/tap已经可以使用； 如果结果是：cat: /dev/net/tun: No such device 或其他则说明tun/tap没有被正确配置，要不打开tun/tap，要不就是VPS不支持。
</p>
<p>
	2.先安装iptables模块，如果已安装则跳过<pre class="prettyprint linenums">apt-get install iptables</pre>
</p>
<p>
	3.因为搬瓦工的VPS是OpenVZ的，所以设置转发命令如下（11.22.33.44替换为自己的VPS的IP）<pre class="prettyprint linenums">iptables -t nat -A POSTROUTING -s 10.168.0.0/16 -j SNAT --to-source 11.22.33.44</pre>
</p>
<p>
	4.安装OpenVPN，同时需要lzop模块<pre class="prettyprint linenums">apt-get install openvpn lzop</pre>
</p>
<p>
	5.然后使用easy-rsa生成证书
</p>
<p>
	默认OpenVPN的easy-rsa文档会在/usr/share/doc/openvpn/examples/easy-rsa/，如果不在的话请先检查是否安装成功然后用locate或find命令查找该文档。然后将该文档下所需的配置文件复制到/etc/openvpn/下面
</p>
<pre class="prettyprint linenums">cp -r /usr/share/doc/openvpn/examples/easy-rsa/ /etc/openvpn/</pre>
<p>
	生成CA证书
<pre class="prettyprint linenums">
cd /etc/openvpn/easy-rsa/2.0
source vars
./clean-all
./build-ca
</pre>
</p>
<p>
	注意看提示信息，一般直接回车即可
</p>
<p>
	6.然后再生成服务器证书和客户证书
<pre class="prettyprint linenums">./build-key-server server</pre>出现的提示信息一般直接回车即可，最后有一块要输入y[yes]
<pre class="prettyprint linenums">./build-key client</pre>出现的提示信息一般直接回车即可，最后有一块要输入y[yes]
</p>
<p>
	所有生成的证书和密钥存都放在/etc/openvpn/easy-rsa/2.0/keys/下面。
</p>
<p>
	生成Diffie Hellman参数（我也不知道干嘛的）<pre class="prettyprint linenums">./build-dh</pre>
</p>
<p>
	7.配置OpenVPN服务器端 
</p>
<p>
	编辑/etc/openvpn/server.conf 文件，如果没有可以创建一个<pre class="prettyprint linenums">vi /etc/openvpn/server.conf</pre>
	加入下面的内容
<pre class="prettyprint">
local 11.22.33.44&nbsp;&nbsp;&nbsp; #11.22.33.44为VPS的IP<br />
port 8080&nbsp;&nbsp;&nbsp; #端口，需要与客户端配置保持一致，并保证与其他软件无共用<br />
proto udp&nbsp;&nbsp;&nbsp; #使用协议，需要与客户端配置保持一致<br />
dev tun&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; #也可以选择tap模式<br />
ca /etc/openvpn/easy-rsa/2.0/keys/ca.crt<br />
cert /etc/openvpn/easy-rsa/2.0/keys/server.crt<br />
key /etc/openvpn/easy-rsa/2.0/keys/server.key<br />
dh /etc/openvpn/easy-rsa/2.0/keys/dh1024.pem<br />
<br />
ifconfig-pool-persist ipp.txt<br />
<br />
server 10.168.1.0 255.255.255.0&nbsp;&nbsp;&nbsp; #给客户的分配的局域网IP段，注意不要与客户端网段冲突！<br />
<br />
push "redirect-gateway"<br />
push "dhcp-option DNS 8.8.8.8"<br />
push "dhcp-option DNS 8.8.4.4"<br />
client-to-client<br />
<br />
;duplicate-cn&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; #若不止一人同时使用该证书，请去掉前面的;<br />
<br />
keepalive 20 60<br />
comp-lzo<br />
max-clients 50<br />
<br />
persist-key<br />
persist-tun<br />
<br />
status openvpn-status.log<br />
log-append openvpn.log<br />
<br />
verb 3<br />
mute 20
</pre>
</p>
<p>
	修改/etc/sysctl.conf的内容
<pre class="prettyprint linenums">
	net.ipv4.ip_forward = 1
	net.ipv4.conf.all.send_redirects = 0
	net.ipv4.conf.default.send_redirects = 0
	net.ipv4.conf.all.accept_redirects = 0
	net.ipv4.conf.default.accept_redirects = 0
</pre>
</p>
<p>
	重新载入/etc/sysctl.conf使其生效，执行如下命令sysctl -p
</p>
<p>
	9.设置开机自动IP转发vi /etc/rc.local加入（11.22.33.44替换为自己的VPS的IP）iptables -t nat -A POSTROUTING -s 10.168.0.0/16 -j SNAT --to-source 11.22.33.44
<pre class="prettyprint linenums">vi /etc/rc.local</pre>
</p>
<p>
	10.部署完毕，重启OpenVPN服务使之生效<pre class="prettyprint linenums">/etc/init.d/openvpn restart</pre>
</p>
<p>
	<br />
</p>
<p>
	<strong>OpenVPN客户端：</strong>
</p>
<p>
	1.这个可是全平台适用，Windows、Mac、Android、iOS通吃。
</p>
<p>
	2.重点是写好配置文件，把各个证书合成一个文件。
</p>
<p>
	3.从服务器上把生成的证书文件下载下来，可以使用<a target="_blank" href="http://winscp.net/eng/download.php">WinSCP</a>建立连接
</p>
<p>
	文件协议SFTP，主机名是VPS的IP，端口是VPS的Port，用户名是root，密码在连接的时候输入
</p>
<p>
	证书文件有三个，都在/etc/openvpn/easy-rsa/2.0/keys/里，把ca.crt、client.crt、client.key下载到本地
</p>
<p>
	4.写配置文件，新建一个文件把以下内容粘贴进去。
<pre class="prettyprint">
client&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; #这个client不是自定义名称 不能更改<br />
dev tun&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; #要与前面server.conf中的配置一致。<br />
proto udp&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; #要与前面server.conf中的配置一致。<br />
remote 11.22.33.44 8080&nbsp;&nbsp;&nbsp; #将11.22.33.44替换为你VPS的IP，端口与前面的server.conf中配置一致。<br />
resolv-retry infinite<br />
nobind<br />
persist-key<br />
persist-tun<br />
ca ca.crt<br />
#cert client.crt<br />
#key client.key<br />
#ns-cert-type server<br />
redirect-gateway<br />
keepalive 20 60<br />
#tls-auth ta.key 1<br />
comp-lzo<br />
verb 3<br />
mute 20<br />
route-method exe<br />
route-delay 2
</pre>
</p>
<p>
	5.把证书文件写入配置文件（推荐使用notepad++）
</p>
<p>
	用notepad++分别打开三个文件，在配置文件末尾加上
<pre class="prettyprint">
&lt;ca&gt;<br />
<br />
&lt;/ca&gt;<br />
&lt;cert&gt;<br />
<br />
&lt;/cert&gt;<br />
&lt;key&gt;<br />
<br />
&lt;/key&gt;
</pre>
分别把对应文件内容粘贴进去
</p>
<p>
	6.保存文件，这样就得到了单文件的OpenVPN配置文件~
</p>
<p>
	7.最后把这个文件发到自己的邮箱里面，就方便调用咯~
</p>
<p>
	<br />
</p>
<br />
<p>{{ page.date | date_to_string }}</p>