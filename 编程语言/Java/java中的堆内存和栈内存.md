# java中的堆内存和栈内存

java中内存也是大致有**栈内存**和**堆内存**  
当代码中定义一些基本类型变量或者引用变量，虚拟机会在**栈**中分配内存  
然后到这个作用域结束，java会自动回收该部分内存  
而当你要new对象或者数组，这块内存将会在**堆**里分配。这块内存会有java虚拟机来自动回收。  
所谓的 **引用变量**，类似于 **指针**，是分配在栈内存当中的，然而指向堆内存。当一个new出来的对象没有再被引用指向的时候，就会被垃圾回收器在不定时清掉。

## 内存分配策略

### 1.静态存储分配
在编译的时候就能确定每个数据目标的存储空间，不允许可变数据结构。
### 2.栈式存储分配
编译时对程序区未知，只有到运行，在进入某一个程序区块时候，才能为其分配内存。栈式内存符合先进后出
### 3.堆式存储分配
堆中的内存无法在编译时和运行时确定，可按任意顺序分配释放