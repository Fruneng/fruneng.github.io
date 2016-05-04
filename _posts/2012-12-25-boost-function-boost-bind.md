---
layout: post
title: "boost function 和boost bind"
description: "boost function 和 boost bind的使用及注意"
category: cpp
tags: [boost, cpp]
---

前两天和同事聊起c/c++宏里面的坑，今天猛然想起`boost::bind`和`boost::function`实现都少不了宏，这两个类里面的坑其实也不少

写了段代码，试说明其中的坑在哪里

{% highlight c++ linenos %}
//============================================================================
// Name        : Ctemplate.cpp
// Author      : cc
// Version     :
// Copyright   : Your copyright notice
// Description : Hello World in C++, Ansi-style
//============================================================================

#include <iostream>
#include <vector>
#include <boost/function.hpp>
#include <boost/bind.hpp>
using namespace std;

template<typename Ty>
class RVector : public std::vector<Ty>
{
public:
	RVector():std::vector<Ty>(){}
	RVector(const RVector& ov)
	{
		std::cout << "RVector copy once!" << std::endl;
		*this = ov;
	}
};

template<typename T>
int do_something(T t)
{
	std::cout << "just do something!" << std::endl;
	return 0;
}


int main() {
	RVector<int> rv;

	std::cout << "test 1" << std::endl;
	do_something(rv);

	std::cout << "test 2" << std::endl;
	do_something(boost::ref(rv));

	std::cout << "test 3" << std::endl;
	boost::function<int ()> func = 
		boost::bind(do_something<RVector<int> >, rv);
	func();

	std::cout << "test 4" << std::endl;
	boost::function<int ()> func2 = 
		boost::bind(do_something<RVector<int>& >, boost::ref(rv));
	func2();

	std::cout << "test 5" << std::endl;
	boost::function<int (RVector<int>&)> func3 = 
		do_something<RVector<int>& >;
	func3(boost::ref(rv));

	return 0;
}
{% endhighlight %}

	test 1
	RVector copy once!
	just do something!
	test 2
	just do something!
	test 3
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	RVector copy once!
	just do something!
	test 4
	just do something!
	test 5
	just do something!


实际部分的坑是在c++函数参数`传值`和`传引用`上 被一系列宏和函数放大了结果 test3中拷贝达到了12次

顺便一提`boost::ref`的实现 这个类使test1到test2减少了一次拷贝 

{% highlight c++ %}
template<class T> class reference_wrapper
{ 
public:
    typedef T type;

#if defined( BOOST_MSVC ) && BOOST_WORKAROUND( BOOST_MSVC, < 1300 )

    explicit reference_wrapper(T& t): t_(&t) {}

#else

    explicit reference_wrapper(T& t): t_(boost::addressof(t)) {}//还是通过指针实现的

#endif

    operator T& () const { return *t_; }//重载了&操作

    T& get() const { return *t_; }

    T* get_pointer() const { return t_; }

private:

    T* t_;
};
{% endhighlight %}
