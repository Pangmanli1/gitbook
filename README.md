# 一个工作学习笔记本
   以前一直习惯用笔纸做笔记,手写代码,这样感觉方便记忆,也有自己独特的味道,瞬间联想到记笔记时的思路.
   但是有时候查询是个大问题,所以决定开始使用Gitbook. 没看什么教程,喜欢探索,比较粗糙,不过能达到轻松记录,方便查询的目的就好
## 单例滥用的坏处 2018-05-20 
    1.单例是披着羊皮的全局的可变状态
    2.在多线程并行的情况下,任何其他地方都可以对改单例做修改
    3.永久态是单元测试的天敌
    
    @interface SPURLCache
     + (SPCache *)sharedURLCache;
    - (void)storeCachedResponse:(NSCachedURLResponse *)cachedResponse forRequest:(NSURLRequest *)request;
    @end 
    
    刚开始:
         step 1: 服务端 (发error消息)-> 客户端 toast error (单例URLCache 缓存失败response)
         step 2: .......
         step 3: 服务端 (发成功的消息)-> 客户端 成功回调处理
    过段时间: 代码未更改, 测试顺序改为 3-> 1 -> 2
         step 3: 服务端 (发成功的消息)-> 客户端 成功回调处理
         step 1: 服务端 (发失败error消息) - > 客户端 成功回调处理(因为单例的URL 缓存了成功的response)
         step 2: ....
     持久化状态是单元测试的敌人，因为单元测试在各个测试用例相互独立的情况下才有效。如果状态从一个测试用例传递到了另外一个，这样就和测试用例的执行顺序就有关系了。有 bug 的测试用例，尤其是那些本来不应该通过的测试用例，是非常糟糕的事情。
    4. 单例对象一旦创建,由于单例对象指针是存放在静态区, 单例对象在堆中分配的内存只有在程序终止后才能被释放,过多的使用会消耗内存. 
    5. 单例应该只用来保存全局的状态，并且不能和任何作用域绑定.如果这些状态的作用域比一个完整的应用程序的生命周期要短,那么这个状态就不能用单例来管理.
#### 单例的两种写法
    1.
    +(instancetype)shared{
    volatile static targetClass *instance = nil;
    if (instance == nil) {
        @synchronized(self) {
            if (instance == nil) {
                instance = [[targetClass alloc] init];
            }
        }
    }
    return instance;
    }
    使用@synchronized 主要是考虑到线程安全,同步创建(即按顺序创建,已创建则不创建,用static修饰,所以全局只能创建一个对象出来). 避免并行可能创建出两个.
    2. GCD dispatch_once运用
    + (instancetype)sharedInstance
    {
        static dispatch_once_t once;
        static id sharedInstance;
        dispatch_once(&once, ^{
              sharedInstance = [[self alloc] init];
        });
        return sharedInstance;
     }
### 参考文章链接: https://objccn.io/issue-13-2/


    

