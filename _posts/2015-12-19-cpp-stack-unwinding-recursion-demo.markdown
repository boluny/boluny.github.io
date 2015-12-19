---
title: "用递归来演示C++的stack unwinding"
layout: post
type: tech
---

之前某天吃晚饭的时间，想到了栈展开（stack unwinding)，也就是throw出来的exception会沿着调用链反向寻找异常处理语句的过程，因为一般函数的调用链实现为栈帧的增长，所以这个过程形象的描述就是栈回溯，以前看书上写的云里雾里，觉得不够直观。想到递归函数可以直观的表达栈的递增过程，同时反向的查找也很好理解，于是写了一段代码来证明：

    #include <iostream>
    #include <cstdlib>

    using namespace std;

    static int i = 0;
    void foo()
    {
        int j = i++;
        if ( j == 5)
        {
            try
            {
                foo();
            }
            catch (exception &e)
            {
                cout << "catch exception " << e.what() << " in function " << __FUNCTION__ <<  " with level " << j << endl;
                exit(-1);
            }
        }
        else if (j == 10)
        {
            cout << "throw exception in function " << __FUNCTION__ <<  " with level " << j << endl;
            throw exception();
        }
        else
        {
            foo();
        }
    }

    int main(int argc, char **argv)
    {
            foo();
            return 0;
    }


可以从代码中看到，我们在递归深度10的时候throw了一个exception，并且在递归深度5的时候设置了捕捉的代码，程序的输出也证明了这一点：

> $ ./a.out
>
> throw exception in function foo with level 10
>
> catch exception std::exception in function foo with level 5
>

用递归来做demo还是比较简单的，毕竟只有这一个函数，栈的增长也是其递归调用的结果，可以很方便的追踪栈轨迹，也就很容易演示栈展开这一特性了，至于如何用setjmp和longjmp来模拟try...catch这个特性，大概就是在每个函数调用栈中都调用一遍setjmp，另外注册一个回调函数指针来指示模拟异常处理语句块，每当出现问题（模拟throw），先查找本栈帧内的异常处理函数指针是否为空，若是则用函数指针指向的函数处理，若否，则longjmp到调用函数的jmp_buf处，恢复其调用现场，执行类似操作，一层层往上直到main函数。

大概如此，希望没有猜错。
