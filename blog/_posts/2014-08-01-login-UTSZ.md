---
title: '模拟登陆UTSZ并保持在线（VBS）'
layout: post
categories:
    - VBS
tags:
    - VBS
---

<p>大学城的网络很坑爹，经常自动掉线，故写这么个VBS脚本随时保持在线~</p>

<p>用户名是校园卡号，密码是经过base64加密过的~</p>
<pre class="prettyprint lang-vb linenums">
On Error Resume Next
REM Ignore error and prevent the vbs killing itself
Dim connect,test,html,check,userid,userpwd,interval
REM Define variables.

REM Please replace the following variables with yours
userid=XXXX
REM Your student ID
userpwd=XXXX
REM Your password encrypted by Base64
interval=30
REM The interval to check connection. (In seconds)

connect="http://219.223.254.55/portal/login.jsp?userName="&userid&"&userPwd="&userpwd&"&language"
REM Set website to connect.
test="http://www.baidu.com"
REM To test whether you are online.

Set html = CreateObject("Msxml2.ServerXMLHTTP")
REM Create a new XMLHTTP variable
Set check = new RegExp
check.pattern = "百度"
REM To grab the keywork to test whether you are online.

do
	html.open "GET", test,false
	if html.send then
		exit do
		REM Not connected to UTSZ, PKU, or Dorm router.
	end if
	if check.test(html.responseText) <> "True" then
		REM	check whether the internet drops.
		html.open "GET", connect,false
		html.send
		REM if it drops, reconnect.
	end if
	wscript.sleep(1000*interval)
	REM wait for 1 minute to recheck.
loop
msgbox "Please check you network connection!"
</pre>

<p>{{ page.date | date_to_string }}</p>