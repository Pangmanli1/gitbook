# iOS Crash收集- 2018-06-01
     项目崩溃是程序员的最大耻辱, 绩效考察的重要指标, 一旦崩溃发生. 修复它成为最紧要的事情,在调试阶段还能在控制台查看调用堆栈. 而对于线上的APP, 我们只能通过 NSSetUncaughtExceptionHandler (&UncaughtExceptionHandler),来自动获取崩溃时的调用栈,并做相应的处理(1. 本地化存储 2,发送带崩溃信息的邮件 3, 发送到我们的服务器)
     
##  1. 苹果自带Crash收集服务
        *打开开发者账号进入 iTunes Connect(Manage Your Applications - View Details - Crash Reports)
        


    

