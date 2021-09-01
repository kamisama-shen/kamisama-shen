关于OC本质的探讨
==
问题一、一个NSObject对象占用多少内存？
--
 思考： OC对象在内存中是如何布局的？<br> 
 
OC代码底层实现是C/C++代码<br> 

OC ->  C/C++  -> 汇编语言 -> 机器语言<br> 

创建一个命令行项目，main.m文件中添加代码<br> 

NSObject *obj = [NSObject alloc] init];<br> 

cd到根目录下 终端执行xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m -o main.cpp
<br> 
在cpp文件中，<br> 
struct NSObject_IMPL {<br> 
    Class isa;<br> 
};<br> 
<br> 
同样 创建一个继承自NSObject的类 并初始化<br> 
@interface KMPerson : NSObject {<br> 
        @public <br> 
        int _age;<br> 
        int _height;<br> 
}<br> 
转为C++文件后<br> 
struct KMPerson_IMPL {<br> 
    struct NSObject_IMPL NSObject_IVARS;<br> 
    int _age;<br> 
    int _height;<br> 
};<br> 

NSObject_IMPL 就是NSObject的内存布局<br> 

同样 如果存在多层继承关系 <br> 
@interface KMStudent : KMPerson {<br> 
        @public<br> 
        int _no;<br> 
            }<br> 
结构又是如何呢？

struct KMStudent_IMPL {<br> 
        struct KMPerson_IMPL KMPerson_IVARS;<br> 
        int _no;<br> 
};<br> 

等价于 <br> 
struct KMStudent_IMPL {<br> 
        struct NSObject_IMPL NSObject_IVARS;<br> 
        int _age;<br> 
        int _height;<br> 
        int _no;<br> 
};<br> 
<br> 
也等价于<br> 
struct KMStudent_IMPL {<br> 
        Class isa;<br> 
        int _age;<br> 
        int _height;<br> 
        int _no;<br> 
};<br> 

问题没解决 新的问题又来了 isa又是什么
--
