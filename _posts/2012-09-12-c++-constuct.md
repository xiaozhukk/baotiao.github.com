---
date: 2012-09-12 07:16:14+00:00
layout: post
title: c++对象模型 构造函数语义学
---

### 1. C++ 不会为所有的class构造默认的constructor

(我们经常认为c++会为所有的class默认添加构造函数) 

只有满足4个条件之一会添加构造函数

1. 包含带有默认构造函数的对象成员的类
2. 继承自带有默认构造函数的基类的类
3. 带有虚函数的类
4. 带一个虚基类的类

这4中情况会导致编译器必须为未申明构造函数的class, 生成一个默认的constructor. 更多情况下是不会去生成默认的构造函数的.

### 2. copy constructor 构造语义学

C++并不是为所有的类都够着copy constructor. 

默认的情况是如果没有定义copy constructor, 那么一般情况下如果有copy constructor 操作, 默认的是执行bitwise copy semantics. 也就是位逐次拷贝. 

当然 bistwise copy 是有条件的,当不满足bitwise copy 的条件的时候,就不能进行bitwise copy. 那么C++就会为该类添加默认的copy constructor. ( 这里我感觉就是深拷贝和浅拷贝的区别,很明显bitwise 是浅拷贝,直接将一个对象的data member 拷贝给同一个类的另一个对象. 而如果这里不能直接内存拷贝,则需要深拷贝.就是需要构造一个copy constructor了)

4种情况不满足bitwise copy. 也就是会调用构造函数

1. 当一个class 内的一个member object,而这个object 声明了一个copy constructor时
2. 当class 继承自一个bass class, 而这个bass class 有 copy constructor
3. 当class申明了一个或多个 virtual functions 时
4. 当class 派生自一个继承串链,其中一个或多个virtual base classes


### copy assign constructor 构造语义学

跟上面的copy constructor 构造语义学一样, 如果能进行浅拷贝就浅拷贝, 不行的时候才会去生成assign operator

### 对象析构语义学. 

只有在基类拥有析构函数,或者object member 拥有析构函数的时候,编译器在为类合成析构函数,否则都被视为不需要.

这几个默认生成的函数都是在需要的时候才会被编译器生成出来, 默认情况下是不会生成这些函数的.所以如果class 当成一个存结构体使用, 效率是和C 的stuct 是一样的
