计算机组成原理 操作系统  汇编语言 这三者其实是密不可分的


Linux如何管理内存

这玩意光看网上的文章真的只能浅尝辄止...

除非系统看书 + 实操一下

https://xie.infoq.cn/article/ecd4c188f597211852844538e





Linux文件系统

。。。



物理内存和虚拟内存

https://zhuanlan.zhihu.com/p/85760403

这块其实还有分页分段的相关知识，引申到逻辑地址，线性地址和物理地址。



为什么进程切换开销比线程大，线程切换的开销比协程大？

进程切换需要切换页表，刷新TLB（缓存），同时需要重新载入数据段和代码段，而同一个进程下多个线程是共享进程资源的，切换线程只需要保存线程运行的上下文，这其实就是更新几个寄存器。

协程是一种用户空间的线程，本质上来说，它可以自己决定什么时候交出cpu而不需要响应时钟阻塞，那么就减少了系统调用的次数。

换句话说，一个线程在调用阻塞式的函数时，如果阻塞就会陷入内核，有内核管理切换线程。但是协程响应阻塞的方式是在用户态交出cpu控制权，这需要协程自己维护执行的上下文。 具体怎么维护因为我自己没有实现过协程，也不是很懂。。。

具体分析 https://www.zhihu.com/question/20511233

有了多线程，为什么还要有协程呢？https://xie.infoq.cn/article/c62beed510625a8e128aa9001



在shell中启动一个进程，按下control+c后发生了什么？

http://docs.linuxtone.org/ebooks/C&CPP/c/ch33s01.html



为什么从用户态切换到内核态的开销比较大？

当从用户态切换到内核态，会发生中断，中断实际上是对cpu的一次请求，cpu收到信号后执行system_call

用户态的函数调用会使用用户栈，但是从用户态切换到内核态执行系统调用时使用的是内核栈，同时还会切换一系列寄存器，这种切换一定程度上破坏了局部性原理，使得原来的缓存失效。除了环境切换以外，系统还要执行安全性和一致性的检查。



说一说mmap？

mmap是用户空间到文件的映射。

先来看常规的读写磁盘文件的过程。上层应用程序通过调用read/write系统调用进入内核态，内核检查已经打开的文件描述符表，找到对应的inode（index node 存储文件信息，如创建人，磁盘位置等），如果对应的文件在缓存中 则直接返回，否则根据inode定位到磁盘上，把磁盘文件写入缓存，由于页缓存在内核空间，所以此时还不能被用户进程访问。必须再次把内核中的数据拷贝到用户空间，才能被进程访问。

而mmap，是在用户的逻辑地址空间找到一段连续内存，把它和磁盘上一个文件/对象建立映射，当进程访问数据时，发生缺页中段，cpu会把数据从磁盘拷贝到建立映射的逻辑地址空间里。 所以整个过程只发生了一次数据拷贝。

mmap可以用来做进程间的通信，通信的进程把自己的用户空间映射到同一个区域即可。





linux是怎么管理内存的？

。。。



硬链接和软链接？

https://www.jianshu.com/p/dde6a01c4094



多个进程同时操作同一个文件时会发生什么？