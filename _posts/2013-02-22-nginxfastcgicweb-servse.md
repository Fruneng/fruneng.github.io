---
layout: post
title: "nginx+fastcgi+c开发webservse"
description: ""
category: cpp
tags: [cpp]
---


  最近尝试用ngnix+fastcgi+c开发webservse。记录下学习的过程。
  要安装的程序及配置

  1. nginx

	sudo apt-get install nginx-full

配置文件/etc/nginx/conf/nginx.conf:
	
	...
	server {
		listen   8089;
		server_name hello;

		location /do {
			root   /home/www;
			index  index.cgi;
			fastcgi_pass 127.0.0.1:9000;
					
			fastcgi_param  QUERY_STRING     $query_string;
			fastcgi_param  REQUEST_METHOD   $request_method;
			fastcgi_param  CONTENT_TYPE     $content_type;
			fastcgi_param  CONTENT_LENGTH   $content_length;
		}
	}
	...

  2. spawn-fcgi

	sudo apt-get install spawn-fcgi

  3. fcgi2.4.0

	wget http://www.fastcgi.com/dist/fcgi.tar.gz
	tar -zxvf fcgi.tar.gz
	cd fcgi-2.4.0
	./configure
	make
	sudo make install


c fastcgi demo
{% highlight c %}
#include <stdlib.h>

#ifdef HAVE_UNISTD_H
#include <unistd.h>
#endif

#include "fcgiapp.h"
#include <string.h>
#include <string>

std::string getPara(std::string name, const std::string& src)
{
    //TODO we need a more effective and more correct implement
    std::string substr("");
    std::size_t pos = src.find(name);
    if(pos != std::string::npos)
    {
	std::size_t start = src.find('=', pos) + 1;
	std::size_t end = src.find('&', pos);
	substr = src.substr(start, end - start);
    }
    return substr;
}

int main ()
{
    FCGX_Stream *in, *out, *err;
    FCGX_ParamArray envp;

    while (FCGX_Accept(&in, &out, &err, &envp) >= 0) {
    	char *method = FCGX_GetParam("REQUEST_METHOD", envp);

    	FCGX_FPrintF(out,
		"Content-type: text/html\r\n"
		"\r\n");

    	//do POST method
    	if(strcmp(method, "POST") == 0)
    	{
            char *contentLength = FCGX_GetParam("CONTENT_LENGTH", envp);
            int len = 0;

            if (contentLength != NULL)
                len = strtol(contentLength, NULL, 10);

            if (len <= 0) {
                FCGX_FPrintF(out, "No data from standard input.\n");
            }
            else {
                char* str = new char[len+1];
                FCGX_GetStr(str, len, in);
                str[len] = 0;

                if (str)
                {
                	//TODO your POST method
                	FCGX_FPrintF(out, "this is a post method");
		}
		delete[] str;
    	}
    	//do GET method
    	else if (strcmp(method, "GET") == 0){
    	    char* str = FCGX_GetParam("QUERY_STRING", envp);

            if (str)
            {
            	//TODO your GET method
                FCGX_FPrintF(out, "this is a get method");
            }
	}
    	//do other method
    	else
    	{
    	    FCGX_FPrintF(out, "other method\n");
    	}

    } /* while */

    return 0;
}
{% endhighlight %}

特别注意在高并发下 spawn-fcgi 会有一个no response hangs的bug[参考资料](http://tech.gaeatimes.com/index.php/archive/how-to-stop-crashing-hanging-of-php-cgi-spawn-fcgi-with-nginx-lighttpd/)在php开发时用cgi的一个最大连接限制避免这个bug，
，本人尝试使用nginx的unix domain socket来避免这个bug(也可尝试在fcgi库中配置最大cgi连接)，分布部署时nginx只能和cgi部署于同一台机器，再用另一台nginx做端口映射和负载均衡。
具体配置如下
ngnix.conf:

	#fastcgi_pass 127.0.0.1:9000;
	fast_psss unix:/home/www/fastcgi.sock
	
spawn-fcgi启动参数
	
	spawn-fcgi -a 127.0.0.1 -s /home/www/fcgi.sock -u www-data -g www-data -F 8 ./helloCGI

参数`-u`,`-g`是用户和组与nginx相同 具体参看[nginx.org](http://nginx.org/cn/)和`spawn-fcgi --help`
  
