*内存泄漏的定义为当程序不再需要的内存，由于某种原因其不会返回到操作系统或可用内存池，内存泄漏会导致一系列问题,比如运行缓慢，崩溃，高延迟等*

常见的内存泄露：
* 意外的全局变量
* 遗忘的计时器或回调函数
* 脱离文档的DOM引用
* 闭包