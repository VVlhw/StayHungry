# Redis面试题整理

?> 待整理的redis面试题资料：

- [ ] https://blog.csdn.net/Butterfly_resting/article/details/89668661 
- [ ] https://www.cnblogs.com/jasontec/p/9699242.html 
- [ ] https://zhuanlan.zhihu.com/p/93515595



> Redis 是单线程的！为什么单线程还这么快？

明白Redis是很快的，官方表示，<mark>Redis是基于内存操作，CPU不是Redis性能瓶颈，Redis的瓶颈是根据机器的内存和网络带宽  </mark>，既然可以使用单线程来实现，就使用单线程了！所以就使用了单线程了！
Redis 是C 语言写的，官方提供的数据为 100000+ 的QPS，完全不比同样是使用 key-vale的Memecache差！
Redis 为什么单线程还这么快？
1、误区1：高性能的服务器一定是多线程的？
2、误区2：多线程（<mark>存在CPU上下文切换！ </mark>）一定比单线程效率高！

速度：`CPU>内存>硬盘`
**核心：redis 是将所有的数据全部放在内存中的，所以说使用单线程去操作效率就是最高的，多线程（CPU上下文会切换：耗时的操作！！！），对于内存系统来说，如果没有上下文切换效率就是最高的！多次读写都是在一个CPU上的，在内存情况下，这个就是最佳的方案！**



