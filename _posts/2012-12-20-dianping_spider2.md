---
layout: post
title: "抓取大众点评数据2"
description: ""
category: java
tags: [java]
---

接着昨天的数据采集 此次根据店铺id即昨天采集的data文件进入店铺页面采集详细数据

dzdp2.jar 处理data文件中的数据并将数据存入数据库

数据库结构如图

<div style="align: 'left';">
<img src= "/assets/pic/2012-12-20_db_struct.png" 
    style= "style=width: 100%;">
</div>



java重写的解码网页上POI字段的算法

{% highlight java %}
public class ParsePOI {
	static public Gps parse(String s)
	{
		int digi=16;  
		int add= 10;  
		int plus=7;  
		int cha=36;  
		int I = -1;  
		int H = 0;  
		String B = "";  
		int J = s.length();  
		char G =  s.charAt(J-1);
		s = s.substring(0, J - 1);  
		J--;  
		for (int E = 0; E < J; E++)
		{  
			int D = Integer.parseInt(
				char2String(s.charAt(E)), cha) - add;  
			if (D >= add) {  
				D = D - plus; 
			}  
			B += Integer.toString(D, cha);
			if (D > H) {  
				I = E;  
				H = D;
			}  
		}  
		int A = Integer.parseInt(B.substring(0, I), digi);  
		int F = Integer.parseInt(B.substring(I + 1), digi);  
		int tg = Integer.parseInt(char2String(G),cha);
		double L = (A + F - tg) / 2.0;  
		double K = (F - L) / 100000;  
		L /= 100000;  
	
		return new Gps(L,K);
	}

	static private String char2String(char c)
	{
		String ret = "";
		ret += c;
		return ret;
	}

	public static void main(String[] args) 
	{ 

		Gps gps = ParsePOI.parse("IJDCWBZVFIDCFN");
		System.out.println(gps.toString());
	}
}
{% endhighlight %}
提供项目源码下载[dzdp.tar.gz](/assets/file/dzdp.tar.gz)

[dzdp.jar](/assets/file/dzdp.jar)

[dzdp2.jar](/assets/file/dzdp2.jar)

[dzdp.db3](/assets/file/dzdp.db3)
