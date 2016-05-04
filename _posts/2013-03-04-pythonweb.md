---
layout: post
title: "采用python进行web功能测试"
description: ""
category: python
tags: [python]
---



测试方案

本方案为服务器整体功能测试方案，测试采用python语言为脚本语言


依赖库

1.python 2.7

		sudo apt-get install python

2.[python-requests](http://docs.python-requests.org/en/latest/)
	
		pip install requests


测试示例

{% highlight python %}
import time
import requests
r = requests.get('https://github.com/timeline.json') #构造请求
print r.text #打印收到的文本
time.sleep(0.1) #休眠0.1秒
{% endhighlight %}


编写完整的流程脚本对服务器进行黑盒测试 所得结果结合diff工具比对