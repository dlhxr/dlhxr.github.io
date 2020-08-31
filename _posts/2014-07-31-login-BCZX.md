---
title: '模拟登陆BCZX并抓数据（Python）'
layout: post
categories:
    - Python
tags:
    - Python
---

<p>最近学习python，写了个模拟登陆BCZX的脚本，方便工作~</p>

<p>首先import需要的库，蛮多的~</p>
<pre class="prettyprint linenums">
import re,cookielib,urllib,urllib2,os,sys,csv,codecs,time
</pre>

<p>定义需要的变量，当然用户名密码此处匿。</p>
<pre class="prettyprint linenums">
#define useful variables
Username = "XXXX"
Password = "XXXX"
#Final CSV file location
pathr = 'D:/desktop/BCZX.csv'
pathw = 'D:/desktop/temp.csv'
</pre>

<p>登陆BCZX</p>
<pre class="prettyprint linenums">
def loginBCZX():
    #install cookies
    cj = cookielib.CookieJar()
    cookies = urllib2.HTTPCookieProcessor(cj)
    opener = urllib2.build_opener(cookies)
    urllib2.install_opener(opener)
	
    MainLoginUrl = "http://www.baiinfo.com/Account/TopPart"
    
    #post data
    postDict = {
        'LogName'       : Username,
        'password'      : Password,
        'RememberMe'    : "false",
    }
    postData = urllib.urlencode(postDict)
    
    #login
    req = urllib2.Request(MainLoginUrl, postData)
    req.add_header('Content-Type', "application/x-www-form-urlencoded")
    resp = urllib2.urlopen(req)
    respHtml = resp.read()
</pre>

<p>得到当前日期和前一个月的第一天，因为具体数据的url需要这个</p>
<pre class="prettyprint linenums">
def getdate():
    currentdate = time.strftime("%Y-%m-%d")
    year = time.strftime("%Y")
    month = time.strftime("%m")
    if int(month)!=1:
        if int(month)>11:
            lastdate = year + '-' + str(int(month)-1) + '-01'
        else:
            lastdate = year + '-0' + str(int(month)-1) + '-01'
    else:
        lastdate = str(int(year)-1) + '-12-01' 
    return currentdate,lastdate
</pre>

<p>先创建文件，拿甘氨酸作为第一个物种</p>
<pre class="prettyprint linenums">
def Glycine(writer):
    #data page
    userMainUrl = "http://www.baiinfo.com/Orders/NewHistoryGrid?begin=" + getdate()[1] + "&end=" + getdate()[0] + "&skuid=19679%2C&productID=1317"
    
    req2 = urllib2.Request(userMainUrl)
    resp2 = urllib2.urlopen(req2)
    respHtml2 = resp2.read()
    pattern =re.compile('\\\u003e\\\u003ctd\s+?\\\u003e(.*?)\\\u003c/td\\\u003e\\\u003ctd\s+?\\\u003e(.*?)\\\u003c/td\\\u003e\\\u003c/tr\\\u003e\\\u003ctr', re.DOTALL)
    foundData = pattern.findall(respHtml2)
    if(foundData):
        writer.writerow(['','','','','','甘氨酸'])
        for eachitem in foundData:
            date=eachitem[0]
            price=eachitem[1]
            print date
            print price
            writer.writerow(['','','','',date,price])
</pre>

<p>获取其他物种数据，写入BCZX.csv</p>
<pre class="prettyprint linenums">
def getprice(pathr,pathw,number,name):
    userMainUrl = "http://www.baiinfo.com/Orders/NewHistoryGrid?begin=" + getdate()[1] + "&end=" + getdate()[0] + number

    req2 = urllib2.Request(userMainUrl)
    resp2 = urllib2.urlopen(req2)
    respHtml2 = resp2.read()   

    pattern =re.compile('\\\u003e\\\u003ctd\s+?\\\u003e(.*?)\\\u003c/td\\\u003e\\\u003ctd\s+?\\\u003e(.*?)\\\u003c/td\\\u003e\\\u003c/tr\\\u003e\\\u003ctr', re.DOTALL)
    foundData = pattern.findall(respHtml2)
    if(foundData):
        csvfilew = file(pathw, 'wb')
        csvfilew.write(codecs.BOM_UTF8)
        writer = csv.writer(csvfilew,dialect='excel')
        csvfiler = file(pathr)
        reader = csv.reader(csvfiler)
        for row in reader:
            if reader.line_num == 1:
                row.extend(['',name])
                writer.writerow(row)
                continue
            if reader.line_num-2 < len(foundData):
                eachitem = foundData[reader.line_num-2]
                date=eachitem[0]
                price=eachitem[1]
                print date
                print price
                row.extend([date,price])
            writer.writerow(row)
        csvfilew.close()
        csvfiler.close()
        os.remove(pathr)
        os.rename(pathw,pathr) 
</pre>

<p>主函数，先登陆，再获取甘氨酸数据，最后获取其他物种数据</p>
<pre class="prettyprint linenums">
def main():

    loginBCZX()
    
    Glycine(csv.writer(file(pathr, 'wb'),dialect='excel'))
    webpagenum=['&skuid=19679%2C&productID=1317','&skuid=27057%2C&productID=1848','&skuid=21999%2C&productID=1543','&skuid=24830%2C&productID=1715','&skuid=3339%2C&productID=526','&skuid=8156%2C&productID=526','&skuid=21845%2C&productID=1501','&skuid=1097%2C&productID=73','&skuid=8350%2C&productID=1140','&skuid=8184%2C&productID=1134']
    speciesname=['甘氨酸','百草枯','动力煤','无烟末煤','沥青（内）','沥青（外）','萤石粉','蒽油 ','硫酸巨化98%','硝酸铵兴化颗粒']
    for i in range(len(speciesname)-1):
        getprice(pathr,pathw,webpagenum[i+1],speciesname[i+1])
</pre>

<p>最后再执行main()即可~</p>

<p>{{ page.date | date_to_string }}</p>