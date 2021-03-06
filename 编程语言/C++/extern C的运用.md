## extern C
其实在现代C++的书写规范里，也有提到，C和C++语言的混用，我们要经常用extern C把C函数括起来。

到底是为什么需要这样做呢？

根据操作系统下编译链接相关的笔记中，我们知道了编译源代码分几个步骤，大步骤就是编译和链接。

所谓编译，就是把源代码分析成目标代码，并且定义一些符号。符号可以理解为函数地址的一些别名。当我们执行链接操作的时候，就是调用函数就是根据这些别名来找到具体的地址。

C和C++编译的时候，一个关键的地方就是，C语言不会对函数名字进行**重整**。

**C++为了支持函数重载，将编译后的函数名做了重整（mangled name），比如下面的函数**

``` c++
int add(int a, int b) ;
```

在C中编译完的名字就是add，而在C++中，编译完就变成了add_int_int(举例而已，实际因编译器而异)，这样在函数名字后面加上参数的类型，就可以区分不同的重载函数了,通过不同的符号就能实现重载了。

所以当我们引入一个C语言编译的库的时候，我们很可能会调用到这个C语言的函数名，因此，我们在调用这个函数的时候，用C++的方式去调用这个函数显然是找不到地址的，所以我们要告诉编译器，这个函数名要用C语言编译的方式去找：

``` c++
ifdef __cplusplus
extern "C" {

endif
void memset(void, int, size_t);

ifdef __cplusplus
}

endif
```

这里我们还利用了一个宏，__cplusplus是C++编译器定义的一个宏，如果这份代码和C++一起编译，那么memset会在extern "C"里被声明，如果是和C代码一起编译则直接声明，由于__cplusplus没有被定义，所以也不会有语法错误。这样的技巧在系统头文件里经常被用到。


除此之外，我们在引入C的源文件的时候也可以这么做：

``` c++
#ifdef _cplusplus

extern "C" {

#endif

#include "CClass.h"

#ifdef _cplusplus

}

#endif
```