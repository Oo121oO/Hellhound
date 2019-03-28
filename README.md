# Hellhound

这里主要是总结平时工作中主要学习和使用到的技术，部分开源出来，作为技术交流。

## 1、地狱犬系统：
用于监控Android系统中的各种行为，包括页面行为(包括生命周期)、控件行为(点击、长按等)、以及方法(method)级别的执行监控等，技术方案主要涉及静态插桩方案和动态hook方案：
### 1.1、class字节码插桩技术
利用Gradle Plugin、Transform侵入App Build过程，再使用ASM框架，对javac后(混淆任务执行前)的class节码进行插桩；
需要具备的知识点：App构建过程、Gradle插件化、class字节码文件格式(尤其是常量池这个大总管、还有method_info中的Code属性所代表的方法体)、jvm指令语法、基于操作数栈概念(*尤其需要注意，对象方法的this指针用于在局部变量表的slot-0位置，类方法无需这个this指针，且局部变量表存储顺序一般是this指针、接着是参数列表按从左到右顺序存储、之后是方法中声明的局部变量，也是按照声明顺序存储的，所以我们在使用jvm汇编指令插桩时非常容易获取参数，只要会数数就行了，同时为了性能，也可以不设置自动获取局部变量表和栈帧大小，直接数数来手工设置亦可，我们在使用jvm汇编指令调用方法时，也是按照这个顺序从局部变量表中取数据push到栈中来匹配调用方法的形参的，然后才是invoke方法*)、jvm内存模型(*尤其是线程栈、方法帧栈、局部变量表，运行时栈是以线程为单位分配的，栈帧是以方法为单位分配的，可以看出来n个栈帧组成了栈，n亦是方法数，局部变量表、操作数栈存在于栈帧中*)等基础知识。
### 1.2、hook动态代理技术
需要具备的知识点：App启动过程、深入理解四大组件(尤其是Activity)生命周期执行过程(App进程中的ActivityManagerProxy/IActivityManager；System Server进程中的AMS、PMS(暂时没用到，但是也需要熟悉，这样可以做到放心)、ActivityThread、mH等基础知识点、概念)、动态代理技术、hook技术，性能调优、crash栈分析、机型适配等。


## 2、版本计划
- version1.0：劫持控件操作(点击、长按、滑动、拖动等)行为
- version1.1：劫持Activity生命周期，并支持劫持和注入Activity跳转时Intent中传递的数据
- version1.2：支持高性能跨进程组件：共享内存，自此以后，Hellhound开始支持了跨进程。
- version1.3：劫持Fragment生命周期
- version1.4：劫持通知栏行为
- version2.0：劫持业务层数据的技术方案

## 3、进度
工作比较忙，目前进度还在version1.0中，目前已经整理并完成了控件点击和Activity生命周期的插桩，后续尽快补上其他操作手势的插桩(虽然原理都一样)，和更合理完善的代码！
- 最新进度：Activity生命周期监控、startActivity/finish/moveTaskToBack方法监控，并抓取调用者、目标对象，以及Intent等传递的参数数据，回调出来，从而可以形成页面链路；View监控点击的部分已完成，接下来是监控其他手势。
