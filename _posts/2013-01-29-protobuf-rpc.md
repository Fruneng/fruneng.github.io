---
layout: post
title: "protobuf RPC"
description: "protobuf RPC 框架的初步学习"
category: cpp
tags: [cpp]
---


最近准备在框架中使用RPC，粗粗看了下几种RPC的使用，为了效率准备使用一种保持长链接的RPC。之前看到protobuf官网上提到有protobuf实现的RPC特别留意了一下，几翻比较下选择了RCF。
  
RCF是c++实现的一个RPC，用到了boost::asio,protobuf等库都是平常常用的一些库。所以准备使用并仔细研读的

pb官网实现的RPC页[https://code.google.com/p/protobuf/wiki/ThirdPartyAddOns](https://code.google.com/p/protobuf/wiki/ThirdPartyAddOns)

RCF官网[http://www.deltavsoft.com/doc/](http://www.deltavsoft.com/doc/)

  一个简单的例子

Person.proto:

	message Person {
	    required int32 id = 1;
	    required string name = 2;
	    optional string email = 3;
	}

	message PbEmpty {
	    optional string log = 1;
	}


main.cpp
{% highlight c++ %}
#define RCF_USE_PROTOBUF

#include <RCF/RCF.hpp>
#include "Person.pb.h"

#include <iostream>

/*声明了三种接口                             		*/ 
/*以RCF_METHOD_XX命名 V表示void R表示有返回值		*/
/*数字表示参数的数量                         		*/
/*client和server都要引用此段声明				*/
RCF_BEGIN(I_HelloWorld, "I_HelloWorld")
    RCF_METHOD_V1(void, Print, const std::string &)
    RCF_METHOD_V1(void, Print, const Person &)
    RCF_METHOD_R2(int, Print, const Person &, Person &)
RCF_END(I_HelloWorld)


/*远程服务器代码的实现*/
class HelloWorldImpl
{
public:
    void Print(const std::string & s)
    {
        std::cout << "I_HelloWorld service: " << s << std::endl;
    }

    void Print(const Person& person)
    {
        // std::cout << person.id() << std::endl
        //     << person.name() << std::endl
        //     << person.email() << std::endl;
    }

    int Print(const Person& in, Person& out)
    {
        // std::cout << person.id() << std::endl
        //     << person.name() << std::endl
        //     << person.email() << std::endl;
    	out = in;

    	return 1;
    }
};


int main()
{
    RCF::RcfInitDeinit rcfInit;

    HelloWorldImpl helloWorld;
    RCF::RcfServer server( RCF::TcpEndpoint(50001) );
    server.bind<I_HelloWorld>(helloWorld);
    server.start();

    RcfClient<I_HelloWorld>( RCF::TcpEndpoint("127.0.0.1", 50001) ).Print(std::string("123"));

    Person person;
    Person reslut;
    person.set_id(1);
    person.set_name("zunceng");
    person.set_email("zunceng@gmail.com");
    reslut.set_id(0);
    reslut.set_name("");
    RCF::TcpEndpoint tcp("127.0.0.1", 50001);
    RcfClient<I_HelloWorld> rcfclient(tcp);


    try{
	rcfclient.Print(person, reslut);
        std::cout << reslut.id() << std::endl
			 << reslut.name() << std::endl
			 << reslut.email() << std::endl;
    }
    catch(std::exception &e)
    {
        std::cout << e.what() << std::endl;
    }
 
    std::cout << "Press Enter to exit..." << std::endl;
    std::cin.get();

    return 0;
}
{% endhighlight %}

RCF使用比较灵活远程调用当法也比较方便
