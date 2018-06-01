# iOS Crash收集- 2018-06-01
     项目崩溃是程序员的最大耻辱, 绩效考察的重要指标, 一旦崩溃发生. 修复它成为最紧要的事情,在调试阶段还能在控制台查看调用堆栈. 而对于线上的APP, 我们只能通过 NSSetUncaughtExceptionHandler (&UncaughtExceptionHandler),来自动获取崩溃时的调用栈,并做相应的处理(1. 本地化存储 2,发送带崩溃信息的邮件 3, 发送到我们的服务器)
     
##  1. 苹果自带Crash收集服务
        a.查看iTunes connect统计信息.  打开开发者账号进入 iTunes Connect(Manage Your Applications - View Details - Crash Reports)
        ![itunes app分析](https://raw.githubusercontent.com/Pangmanli1/gitbook/master/Images/nested/app分析.jpg)
        b.通过xcode定位 a中统计版本的 bug发生的具体代码位置. 通过统计bug 发生的版本号数量等信息, 打开xcode中对应的版本的崩溃日志(xcode -> window -> organizer -> crashes),将鼠标挪动到崩溃的地方出现箭头,点击定位.
        以上收集crash功能需要用户打开 设置-> 隐私 -> 分析 -> 共享iPhone 分析 -> 与应用开发者共享 然后才能自动发送
        
##  2. 自己实现应用内崩溃收集,并上传服务器
        * NSSetUncaughtExceptionHandler
        在app启动完成后利用系统提供的异常捕获类来监听崩溃, NSSetUncaughtExceptionHandler (&UncaughtExceptionHandler), UncaughtExceptionHandler是自定义的函数指针,崩溃发生后, 这个回调函数会传入一个NSException的类. 我们可以利用它来获取调用栈,崩溃的原因及异常的类型. 
     '''
                - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           
           NSSetUncaughtExceptionHandler(&UncaughtExceptionHandler);
           
           return YES;
           }
           
           
           void UncaughtExceptionHandler(NSException * exception){
           
           NSArray * exceptionSymbols =  exception.callStackSymbols;
           
           NSString * reason = exception.reason;
           
           NSString * name  = exception.name;
           
           NSLog(@"exceptionCallSymbols---%@, reason--- %@  ----name %@",exceptionSymbols,reason,name);
           
           //获取到崩溃日志后,可以将崩溃信息存入本地,下次程序启动时将崩溃日志通过邮件发送给开发者或者上传到服务器            }
     '''
        * Dsym
        a. 这样我们获取到的信息是 (Partially Symbolicated),部分符号化的. 接下来要将其完全符号化. (堆栈跟踪是自下而上)展示的, 最先调用的方法在最下面). 完全符号化要借助 dsym 文件, 通过 xcode -> window -> organizer -> 找到对应的包 -> show in finder -> 打开包内容,拷贝出来.   
        b. atos
        然后使用atos命令完成符号化,具体命令:$ atos -arch <Binary Architecture> -o <Path to dSYM file>/Contents/Resources/DWARF/<binary image name> -l <load address> <address to symbolicate>其中: 
        Binary Architecture: arm64、armv6、armv7 armv7s 项目架构。
        Path to dSYM file: dSYM文件的路径。
        binary image name: 工程的名字。
        load address: 基地址，如果我们的崩溃日志中没有这个信息，就需要我们手动去计算:load address = address to symbolicate - offset,比如：0x0000000102838119转化为十进制为4337139993,再减去偏移量265,为4337139728,在转化为十六进制0x0000000102838010
        address to symbolicate:当前方法的内存地址。
        只是命令就会输出符号化的信息. 
##  3. 使用第三方收集
        bugly,友盟, talking data, flurry, bugHD. 原理是对系统产生的Crash日志进行提取,封装, 然后上传到他们的服务器做统计处理.
        优点:集成方便快速, 一般都有完善的管理面板,并已经完全符号化.
        注意点: 引入多个会导致 NSSetUncaughtExceptionHandler()函数指针被覆盖. 这个函数是将函数指针当参数传递, 只要重复调用就会被覆盖.


    

