关于OC本质的探讨
==
问题一、一个NSObject对象占用多少内存？
--
 思考： OC对象在内存中是如何布局的？<br> 
 
OC代码底层实现是C/C++代码<br> 

OC ->  C/C++  -> 汇编语言 -> 机器语言<br> 

创建一个命令行项目，main.m文件中添加代码<br> 
```cpp
NSObject *obj = [[NSObject alloc] init]; 
```
cd到根目录下 终端执行xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m -o main.cpp
<br> 
在cpp文件中，<br> 
```cpp
struct NSObject_IMPL {
    Class isa;
}; 
```

同样 创建一个继承自NSObject的类 并初始化<br> 
```cpp
@interface KMPerson : NSObject {
    @public
    int _age;
    int _height; 
}
```
转为C++文件后
```cpp
struct KMPerson_IMPL { 
    struct NSObject_IMPL NSObject_IVARS; 
    int _age;
    int _height;
};
```

NSObject_IMPL 就是NSObject的内存布局<br> 

同样 如果存在多层继承关系
```cpp
@interface KMStudent : KMPerson {
    @public
    int _no;
}
```
结构又是如何呢？
```cpp
struct KMStudent_IMPL {
    struct KMPerson_IMPL KMPerson_IVARS;
    int _no; 
};
```
等价于 
```cpp
struct KMStudent_IMPL {
    struct NSObject_IMPL NSObject_IVARS;
    int _age;
    int _height;
    int _no; 
};
``` 
也等价于
```cpp
struct KMStudent_IMPL {
    Class isa;
    int _age;
    int _height;
    int _no;
};
```

问题没解决 新的问题又来了 isa又是什么
--
了解isa之前，需要知道OC对象主要分为3种
>> 实例对象 instance对象
>> 类对象 class对象
>> 元类对象 meta-class对象

instance对象 是通过类allco出来的对象，每次调用alloc都会产生新的instance对象

instance对象 在内存中存储的信息
>> isa指针
>>其他成员变量

```cpp
@interface KMPerson :NSObject {
    @public
    int _age;
}

KMPerson *p1 = [[KMPerson alloc] init];
p1->_age = 1;

KMPerson *p2 = [[KMPerson alloc] init];
p2->_age = 3;
```
在内存中 p1 p2的instance对象 的结构分别是

一 一 一 一 一        一 一 一 一 一 
|    instance    |      |    instanc       |
|   一一一一一 |      |   一一一一一 |
|  |      isa      |  |      |   |   isa        |  |
|  |  _age = 1 |  |      |  |_age = 3  |  |
 一 一 一 一 一       一 一 一 一 一 
