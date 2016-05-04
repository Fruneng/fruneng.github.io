---
layout: post
title: "抓取大众点评数据"
description: ""
category: java
tags: [java]
---

最近做了下web数据采集 尝试采集了下google地图和mapabc的数据找不到方法
看了大众点评的网站感觉可以 试着写了点代码抓取网站 

有几点注意点 

<ul>
  <li>1.大众点评网站服务器访问做了限制 http请求要带上浏览器的签名</li>

  <li>2.大众点评网站做访问频率做了限制 可以使用HTTP代理和增加请求延迟来减少和避免http403的错误</li>

  <li>3.大众点评网站数据的加密</li>
</ul>


详细的技术文章可以参考<br>
[http://www.site-digger.com/html/articles/20120621/37.html](http://www.site-digger.com/html/articles/20120621/37.html)<br>
[http://www.site-digger.com/html/articles/20111110/18.html](http://www.site-digger.com/html/articles/20111110/18.html)<br>

这是我写的java抓取大众点评网数据的代码

{% highlight java %}
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.InetSocketAddress;
import java.net.Proxy;
import java.net.URL;
import java.net.URLConnection;
import java.io.*;
import java.util.regex.*;
import java.util.*;
import javax.swing.*;

public class pcap {
	public void getInfoFromUrl(String url,int deep)
	{
		//get web content
		String content = getWebContent(url);

		//get data size
		int data_size = gettotal(content);
	
		System.out.println("download: " + url + " deep: " + deep + " data_size: " + data_size);
	
		//if data_size > 750
		if (data_size > 750 && deep < 4)
		{
			//get url list
			ArrayList<String> url_list = getUrlList(content, deep);
			for(int i = 0; i < url_list.size(); i++)
				getInfoFromUrl(url_list.get(i),deep+1);
		}
		else
		{
			//record
			int request_num = ((data_size<750?data_size:750)-1) / 15 + 1;
			pcapWeb(content);
			for (int i = 2; i < request_num+1; i++)
			{
				String sub_url; 
				if(deep < 2)
					sub_url = url + "/p" + i;
				else
					sub_url = url + "p" + i;
				pcapWeb(getWebContent(sub_url));
				System.out.println("download data: " + sub_url);
			}
		}
	}

	private ArrayList<String> getUrlList(String content, int deep)
	{
		ArrayList<String> url_list = new ArrayList<String>();
		if(deep == 0)
		{
			Pattern p = Pattern.compile("<li><ul class=\"bigCurrent\"><li><strong>全部频道</strong></li>\\s+<li><ul>(.*?)</ul></li>");
			Matcher m = p.matcher(content);
			if(m.find()){
				String sub_content = m.group(1);
				Pattern p2 =  Pattern.compile("href=\"(.*?)\"");
				Matcher m2 = p2.matcher(sub_content);
				while(m2.find())
				{
					String href = domain + m2.group(1);
					url_list.add(href);
				}
			}
			else
			{
				System.out.println("no match!");
			}
		}
		else if (deep == 1)
		{
			Pattern p = Pattern.compile("<li><ul class=\"current\">\\s+<li><strong>.*{0,30}</strong></li>\\s+<li><ul>(.*?)</ul></li></ul></li>");
			Matcher m = p.matcher(content);
			if(m.find()){
				String sub_content = m.group(1);
				Pattern p2 =  Pattern.compile("href=\"(.*?)\"");
				Matcher m2 = p2.matcher(sub_content);
				while(m2.find())
				{
					String href = domain + m2.group(1);
					url_list.add(href);
				}
			}
			else
			{
				System.out.println("no match!");
			}
		
		}
		else if (deep == 2)
		{
			//<ul class="navBlock navTab-cont navTab-cont-on"><li><ul class="bigCurrent">\\s+<li>\\s+<ul>
			Pattern p = Pattern.compile("<ul class=\"navBlock navTab-cont navTab-cont-on\"><li><ul class=\"bigCurrent\">\\s+<li>\\s+<ul>(.*?)</ul>\\s+</li>\\s+</ul>\\s+</li>\\s+</ul>");
			Matcher m = p.matcher(content);
			if(m.find()){
				String sub_content = m.group(1);
				Pattern p2 =  Pattern.compile("href=\"(.*?)\"");
				Matcher m2 = p2.matcher(sub_content);
				while(m2.find())
				{
					String href = domain + m2.group(1);
					url_list.add(href);
				}
			}
			else
			{
				System.out.println("no match!");
			}
		}
		else if (deep == 3)
		{
			Pattern p = Pattern.compile("<li><ul class=\"current\">\\s+<li><strong>.*{0,30}</strong></li>\\s+<li><ul>(.*?)</ul></li></ul></li>");
			Matcher m = p.matcher(content);
			if(m.find()){
				String sub_content = m.group(1);
				Pattern p2 =  Pattern.compile("href=\"(.*?)\"");
				Matcher m2 = p2.matcher(sub_content);
				while(m2.find())
				{
					String href = domain + m2.group(1);
					url_list.add(href);
				}
			}
			else
			{
				System.out.println("no match!");
			}
		}
		else
		{
			System.out.println("so deep!");
		}
		return url_list;
	}
	protected String getWebContent(String url_str)
	{
		while(true)
		{
			long now = new Date().getTime();
			if(now - last_req_time > req_Interval)
			{
				last_req_time = now;
				break;
			}
			try {
				java.util.concurrent.TimeUnit.SECONDS.sleep(1);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	
		StringBuffer sb = new StringBuffer();
		boolean succ = false;
		do
		{
			try {
				java.net.URL url = new java.net.URL(url_str);
				//Proxy proxy = ProxyFactory.getaProxy();
				//URLConnection URLconn = url.openConnection(proxy);
				URLConnection URLconn = url.openConnection();
				URLconn.setRequestProperty("User-Agent",
						"Mozilla/5.0 (compatible; MSIE 5.0; Windows NT; DigExt)\");");
				URLconn.setConnectTimeout(10*1000);
				URLconn.setReadTimeout(30*1000);
				BufferedReader in = new BufferedReader(new InputStreamReader(
				URLconn.getInputStream(),"UTF-8"));
				String line;
				while ((line = in.readLine()) != null) {
					sb.append(line);
				}
				in.close();
				succ = true;
			} catch (Exception e) { // Report any errors that arise
				sb.append(e.toString());
				System.err.println(e);
				create_a_browse("http://www.dianping.com");
			}
		}while(!succ);
		pcap_num++;
		return sb.toString();
	}
	protected void create_a_browse(String url)
	{
		JOptionPane.showMessageDialog( null,"抓包数 :" + pcap_num);
	}

	protected int gettotal(String content)
	{
		String reg = "<span class=\"Color7\">\\((.*?)\\)</span>";
		Pattern p = Pattern.compile(reg);
		Matcher m = p.matcher(content);
		int total;
		if(m.find()){
			total = Integer.parseInt(m.group(1));
		}else
		{
			total = 0;
		}
		return total;
	}

	protected void pcapWeb(String content)
	{
		Pattern p=Pattern.compile("(<ul class=\"detail\">.*?</ul>)"); 
		Matcher mat = p.matcher(content);
		int i = 1;
		while(mat.find())
		{
			record(mat.group());
		}
	}

	private int record_num;
	protected void record(String s)
	{
		try {
			out.write(s);
			out.write("\n");
			record_num++;
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	protected String makeUrl(String root, int citycode)
	{
		return root + citycode + "/0/";
	}

	public pcap()
	{
		record_num = 0;
		Date date = new Date();
		last_req_time = date.getTime();
		try {  
			out = new BufferedWriter(new OutputStreamWriter(  
			new FileOutputStream(file, true)));    
		} catch (Exception e) {  
			e.printStackTrace();  
		} 
	}
	protected void finalize()
	{
		try {
			out.close();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	private String file = "data";
	private BufferedWriter out;
	private String domain = "http://www.dianping.com";
	private long last_req_time;
	private long req_Interval = 5 * 1000;
	private int pcap_num = 0;

	public static void main(String[] args) 
	{	
		if(args.length < 1)
		{
			System.out.println("usage: java -jar xxx http://www.dianping.com/search/category/3/0");
			return ;
		}
	
		pcap p = new pcap();
		p.getInfoFromUrl(args[0], 0);
	}
}
{% endhighlight %}

之后把带gps信息的数据采集下
再者就是低调 低调
