# Basic Note

## 动态编程语言与静态编程语言的区别

- 动态编程语言

	动态语言:(Dynamic programming Language -动态语言或动态编程语言), 动态语言是指程序在运行时可以改变其结构, 
	新的函数可以被引进, 已有的函数可以被删除等在结构上的变化。是在运行时确定数据类型的语言。变量使用之前不需
	要类型声明，通常变量的类型是被赋值的那个值的类型。

	动态类型语言的优点在于方便阅读，不需要写非常多的类型相关的代码；缺点自然就是不方便调试，命名不规范时会造
	成读不懂，不利于理解等。

	常见的动态语言包括：D、JavaScript、ActionScript、Erlang、Groovy、Lisp、Lua、Perl、PHP、Python、Ruby、
	Scala、Smalltalk、Tcl、VBScript等


- 静态编程语言

	静态类型语言:(Statically Typed Language-静态类型语言)静态类型语言与动态类型语言刚好相反, 它的数据类型是
	在编译其间检查的, 也就是说在写程序时要声明所有变量的数据类型。是指在编译时变量的数据类型即可确定的语言，
	多数静态类型语言要求在使用变量之前必须声明数据类型，某些具有类型推导能力的现代语言可能能够部分减轻这个
	要求.

	静态类型语言的主要优点在于其结构非常规范，便于调试，方便类型安全；缺点是为此需要写更多的类型相关代码，
	导致不便于阅读、不清晰明了。

	常见的静态语言包括：c、c++、C#、Java等

## 强类型语言和弱类型语言的区别

- 强类型语言

	是一旦变量的类型被确定，就不能转化的语言。实际上所谓的貌似转化，都是通过中间变量来达到，原本的变量的类
	型肯定是没有变化的。


- 弱类型语言

	一个变量的类型是由其应用上下文确定的。比如语言直接支持字符串和整数可以直接用 + 号搞定。当然，在支持运算
	符重载的强类型语言中也能通过外部实现的方式在形式上做到这一点，不过这个是完全不一样的内涵 