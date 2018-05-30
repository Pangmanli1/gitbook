# 一个工作学习笔记本
   以前一直习惯用笔纸做笔记,手写代码,这样感觉方便记忆,也有自己独特的味道,瞬间联想到记笔记时的思路.
   但是有时候查询是个大问题,所以决定开始使用Gitbook. 没看什么教程,喜欢探索,比较粗糙,不过能达到轻松记录,方便查询的目的就好
# 单例滥用的坏处 2018-05-20 
    1.单例是披着羊皮的全局态可变状态
    2.在多线程并行的情况下,任何其他地方都可以对改单例做修改
    3.永久态是单元测试的天敌
    刚开始:
         step 1: 服务端 (发error消息)-> 客户端 toast error (单例URL 缓存失败response)
         step 2: .......
         step 3: 服务端 (发成功的消息)-> 客户端 成功回调处理
    过段时间: 测试代码被另一个人改为
         step 3: 服务端 (发成功的消息)-> 客户端 成功回调处理
         step 1: 服务端 (发失败error消息) - > 客户端 成功回调处理(因为单例的URL 缓存了成功的response)
         step 2: ....
    4. 单例对象一旦创建,由于单例对象指针是存放在静态区, 单例对象在堆中分配的内存只有在程序终止后才能被释放,过多的使用会消耗内存. 
    5. 真正需要在全局修改某对象的状态时才使用, 如果要保存的全局态 绑定的作用域 小于整个应用的生命周期,那么就不宜采用单例.
