---
title: '强刷联想拯救者Legion Bios救砖'
layout: post
categories:
    - Tutorial
tags:
    - [Tutorial, Bios]
---

首先吐槽一句恶心的联想，发布Bios自己都没认真测试过，41WW风扇巨响，然而降级后电脑直接变砖……官方明明给了降级的功能，变了砖还不维修……

已经看到很多例降级最新的Bios变砖的案例，涉及Y540，Y740等等，我手里的是Y540，即国内的Y7000P 2019，以Y540为例，本文对使用CH341a编程器强刷Bios简单小结一下。

关键点：

    1. 打开笔记本后盖，准确找到Bios芯片位置，同时确定Bios芯片型号。
	2. 购买CH341a编程器及编程夹，国内淘宝一堆，国外亚马逊也有一套的，我买的12刀。
	3. 获取合适的CH341a软件，不同芯片可能存在挑软件现象，需要多尝试。

1. 寻找Bios芯片位置xx

![](/meida/images/2021-04-13-Legion-bios-program/motherboard.png) 

<br>
<p>{{ page.date | date_to_string }}</p>