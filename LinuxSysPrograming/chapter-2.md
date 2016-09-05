## 第二章文件I/O

- 当 write() 调用返回时，内核将所提供的缓冲区数据复制到了内核缓冲区，但却没有保证数据已经写到目的文件，内核进行检查后，将数据拷贝到一个缓冲区，稍后内核收集所有脏缓冲区，排好序，并写入到磁盘上，这使得 write 调用马上可以返回，内核将写入操作推迟到空闲阶段，并将很多写操作一起处理。

- 如果一个 read 调用希望读取刚刚写到缓冲区但尚未写入磁盘的数据，请求将从缓冲区响应，而不是读取磁盘陈旧的数据。提高了效率。

- fsync() 和 fdatasync() 区别在于 fdatasync 只会写入数据，不保证元数据同步。

- sync() 可以对所有缓冲区进行同步，尽管效率不高。

- 在 open() 中使用 O_DIRECT 会使内核最小化 IO 管理的影响，会忽略页缓存机制，直接对用户控件缓冲区和设备进行初始化，IO将是同步的，操作在完成之前不会返回。

- IO 多路复用允许应用在多个文件描述符上同时阻塞，并在其中某个可以读写时受到通知，Linux提供三种方案：select，poll， epoll.

- VFS 虚拟文件系统，允许内核无需了解文件系统类型的情况下，使用文件系统函数和操作文件系统数据，VFS实现这个抽象的办法是使用通用文件模型，是所有Linux文件系统的基础.

- 页缓存通过 /proc/sys/vm/swappiness 来调整，默认是60，较高的值倾向于在内存中保留页缓存。较低的值更倾向于清理页缓存，而不是进行交换。

- 引用局部的另一种形式是空间局部性，关于数据连续使用的性质，内核实现了页缓存预读技术，每次读请求是从磁盘数据中获取更多的数据到页缓存的动作。有一个预读窗口，是动态变化的， 最小 16KB，最大 128KB。