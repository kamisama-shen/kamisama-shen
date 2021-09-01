#关于OC本质的探讨

##问题一、一个NSObject对象占用多少内存？

 思考： OC对象在内存中是如何布局的？
 
OC代码底层实现是C/C++代码

OC ->  C/C++  -> 汇编语言 -> 机器语言

创建一个命令行项目，main.m文件中添加代码

NSObject *obj = [NSObject alloc] init];

cd到根目录下 终端执行xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m -o main.cpp

在cpp文件中，
struct NSObject_IMPL {
    Class isa;
};

同样 创建一个继承自NSObject的类 并初始化
@interface KMPerson : NSObject {
        @public 
        int _age;
        int _height;
}
转为C++文件后
struct KMPerson_IMPL {
    struct NSObject_IMPL NSObject_IVARS;
    int _age;
    int _height;
};

NSObject_IMPL 就是NSObject的内存布局

同样 如果存在多层继承关系 
@interface KMStudent : KMPerson {
    @public
    int _no;
}
结构又是如何呢？
