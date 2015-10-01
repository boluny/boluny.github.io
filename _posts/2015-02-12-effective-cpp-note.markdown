---
title: "《Effective C++》读书笔记&摘抄"
layout: post
type: tech
---

最近在读《Effective C++》,确实是经典之作，但是有的条款也需要一些细节补充，所以都列在这篇文章里，希望能不断更新，个人阅读的是第三版，不包括C++ 11的内容。

## 条款1：视C++为一个语言联邦。

主要可分为4个次语言(sublanguage)，C的部分，OOP的部分，template部分以及标准库STL。都有各自不同的习惯，用哪块就用哪块的规范。

## 条款2：尽量以const, enum, inline替换#define。

这点也没什么好说的，#define只是做单纯的文本展开，其中如果有++x这样的指令会有严重的副作用，所以尽量用其他三者作为其原有功能的替 代，另外c99也认可了#inline，不过还是要明确const定义的常量和#define定义的常量的区别，前者编译时是一个symbol，后者就是 一个字面值。

## 条款3：尽可能使用const。

## 条款4：确定对象被使用前已被初始化。

注意构造函数的初始化列表和赋值的区别，不同编译单元之间的non-local static变量的定义顺序是未定义的，所以最好用local static对象来替代类似的访问呢。

## 条款5：了解C++默默编写并调用哪些函数。

合成的构造函数，copy构造函数，析构函数和copy assignment函数。

## 条款6：若不想使用编译器自动生成的函数，就该明确拒绝。

将相应的合成的成员函数声明为private并且不定义，另外继承一个把copy assignment函数和copy构造函数声明为私有的类可以让编译器在用户欲拷贝派生类时报错。因为基类的copying函数不允许继承类调用，而调 用基类的copying函数是合成的派生类copying函数的默认行为。

## 条款7：为多态基类声明virtual析构函数。

含有virtual成员函数的基类应该也将析构函数声明为virtual的。

## 条款8：别让异常逃离析构函数。

## 条款9：绝不在构造和析构过程中调用virtual函数。

因为在构造和析构函数中，base class构造期间virtual函数绝对不会下降到derived classes阶层。在derived class对象的base class构造期间，对象的类型是base class而不是derived class。一旦derived class析构函数开始执行，对象内的derived class成员便呈现未定义值，所以C++视它们仿佛不再存在，进入base class析构函数后对象就成为一个base class对象。

## 条款10：令operator=返回一个reference to *this。

因为这样方便了链式赋值的处理（a = b = c = d，优先级相同，结合性从右往左，所以 a = (b = (c = d))，标准库)，而operator=是单目操作符重载，其左值隐式参数为*this，即自身，所以return *this收尾，哦这个标准是习惯，非强制，只是觉得还是保持同一个习惯比较好。

## 条款11：在operator=中处理“自我赋值”。

确保函数在操作一个以上的多个对象，而其中许多对象是同一个对象时，其行为保持一致，有3种方法:

1. 比较“来源对象”和“目标对象”的地址，即证同测试：
  
        if(*this == &rhs) return *this;
   
2. 精心安排的语句顺序：
    
        Bitmap* pOrig = pb;
        pb = new Bitmap(*rhs.pb); //如果这里异常了也没事，防的就是这一手
        delete pOrig;
        return *this;
     
3. copy-and-swap技术（不过作者不看好）：
      
        Widget temp(rhs); //把复制动作移到copy构造函数里，也可以pass-by-value
        swap(temp);
        return *this;

## 条款12：复制对象勿忘其每一个部分。

derived中的copying函数（copy构造函数，copy赋值函数）也需要保证base class中的成员也得到复制，就是调用base class中的复制函数。另外写一个copy的私有成员函数供所有copying函数调用即可。

## 条款13：以对象管理资源。

## 条款14：在资源管理类中小心coping行为。

## 条款15：在资源管理类中提供对原始资源的访问。

## 条款16：成对使用new和delete时要采取相同形式。

new对应delete, new []对应delete []。

## 条款17：以独立语句将newed对象置入只能指针。

## 条款18：让接口容易被正确使用，不易被误用。

## 条款19：设计class犹如设计Type。

考虑的因素：

1. 新type的对象应该如何被创建和销毁？
2. 对象的初始化和对象的赋值应该有什么样的差别？
3. 新type的对象如果被passed-by-value，意味着什么？
4. 什么是新type的“合法值”？
5. 你的新type需要配合某个继承图系（inheritance graph)吗？
6. 什么样的操作符和函数对此新type而言是合理的？
7. 什么样的标准函数（默认合成那些构造，copy构造，copy赋值，析构）应该驳回？
8. 谁该取用新type的成员？
9. 什么是新type的“未声明接口”？
10. 你的新tpye有多么一般化？
11. 你真的需要要给新tpye吗？

## 条款20：宁以pass-by-reference-to-const替换pass-by-value。

因为pass-by-value需要调用copy构造函数，如果该类有基类或成员中有类，那也要进行相应的copy构造函数，在函数退出时，会依次 调用相应的析构函数。造成巨大的开销，同时，还会有slicing问题（派生类中不在基类中声明的部分被截掉了）,而且virtual函数调用的是 base类中的对应函数。

## 条款21：必须返回对象时，别妄想返回其reference。

比如乘法需要返回一个新值，那么就得返回对象了。绝不要返回pointer或reference指向一个local stack对象，或返回reference指向一个heap-allocated对象，不然在链式的语句中abc造成内存泄漏。

## 条款22：将成员变量声明为private。

## 条款23：宁以non-member，non-friend替换member函数。

## 条款24：若所有参数皆需类型转换，请为此使用non-member函数。

## 条款25：考虑写出一个不抛异常的swap函数。

标准库有swap函数，但参数对象若为只有一个指针需要复制的类对象，那么标准库的swap是低效率的，而自己写swap函数，用function template， 注意将这个函数模板跟定义类放入同一个命名空间，利用ADL规则可以调用该函数。一般寻找swap函数的规律是先利用ADL寻找参数类对象的类定义命名空 间T专属的swap重载函数（名字与标准库函数模板一样，参数不同），再就是std::swap的标准函数模板。

