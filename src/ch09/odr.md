# 单一定义原则

除了内部连接和外部连接，还有一对概念叫声明和定义。这个我们已经很熟悉了：声明引入名字，而定义使得名字可正常使用。我们在这一章则重点考虑符号的声明和定义。

## 符号的声明和定义

符号是拥有内部或外部连接的函数名或变量名。细数下来，一共就四类：函数、成员函数、全局变量和静态成员数据。

函数和成员函数的声明和定义不用我多说了。定义带有函数体，而不是定义的声明则直接以一个分号结束：
```cpp
// 函数声明
void f();
// 函数定义
void f() {
    // [...]
}

class A {
    // 成员函数一样的道理（不论是否静态）
    // 成员函数声明
    void f();
};
// 成员函数定义
void A::f() {
    // [...]
}
```

另外两种则需要我们稍稍留意一下。静态成员数据有一个特点，我们在[之前](/ch06/static_member)也提到过，就是它出现在成员列表中总是默认是不带定义的声明：
```cpp
class A {
    static int a; // 声明
};
int A::a;         // 定义（可带初始化器）
```

所以区分静态成员数据的声明和定义的最简单方法就是：类内不带初始化器，把定义放在外面。

再来看最后一种：全局变量。全局变量不带定义的声明我们是真没接触过，这里介绍一下：**用 `extern` 修饰且不带初始化器的全局变量声明不是定义**。
```cpp
int a;            // 定义
extern int a;     // 声明
int a{42};        // 定义
extern int a{42}; // 定义（含义同上）
```
当不带初始化器时，`extern` 关键字除了让声明引入的名字成为外部连接外，还使得整个声明不再是定义。也就是说，如果只写 `extern int a;` 就使用 `a` 的话，会导致一个“找不到定义”的错误。

## 单一定义原则

单一定义原则（One Definition Rule, ODR）是一系列规则，规定了 C++ 如何处理声明和定义。我把其中比较重要（也是比较好理解）的三条写在下面：

1. 一个翻译单元中，允许出现一个东西 <sup>※</sup> 的多次声明，但最多只允许出现一次定义；  
<small>※ 指变量、函数、类型和模板</small>
2. 一个翻译单元中，如果 *ODR-使用*了一个符号，那么它最少要出现一次定义。
3. 整个程序中，非*内联*的符号最多只允许出现一次定义；

前两条规则规定了**一个翻译单元中**的定义出现次数。第一条规则说，声明可以多次出现，但定义最多只能有一次：
```cpp
    // 多次声明...
    void f();
    void f();
    // 但定义只能有一个
    void f() { }
//  void f() { } // 取消此行注释报错

    extern int a;
    extern int a;
    int a;
//  int a; // 取消此行注释报错
```

第二条规则说的是在什么时候必须要给出一个定义。在大多数情形下，如果不“使用”这个东西，那么就不需要它的定义。而为了界定“使用”的含义，这里引入了一个术语叫做 ODR-使用（ODR-use）。ODR-使用的正式含义非常复杂，我简单概括为“需要取其地址的行为”。这里还隐含了（变量）读取、写入、（函数）调用以及绑定引用这些情形。
```cpp
// 考察 a 和 c 的使用是否是 ODR-使用
int a;
const int c{42};
int main() {
    a = 42;    // 写入变量，ODR-使用
    int x{a};  // 读取变量，ODR-使用
    int& r{a}; // 绑定引用，ODR-使用

    sizeof(a); // 不求值，不是 ODR-使用
    a;         // 值被抛弃，不是 ODR-使用

    // 读取常量无需取地址，所以不是 ODR-使用
    int y{c};
}
```
当符号被 ODR-使用后，那么就必须要有一个定义。否则，定义不是必需的。所以如果你有一天忘记写 main 函数了，报的不是编译错误，而是链接错误：main 函数被 ODR-使用却找不到定义：
```
ld: ...crt0_c.o:crt0_c.c:(.text.startup+0x2e): undefined reference to `main'
```
典型的链接错误长成上面这样。（注：`ld` 是 GCC 编译链中的链接器的名字。）

> 当然，单一定义原则里也规定了什么时候需要类定义而不是前置声明：我[之前](/ch07/object_relationship)已经提到过这条规则，所以这里不再赘述。

最后一条规则不是针对单个翻译单元的，而是描述了**多个翻译单元在链接时**每个符号允许的定义数量。它说：在大部分情形下最多允许一个符号定义一次，唯有*内联*符号是例外。暂时不管“内联”的情形，先来看看一般的例子：
```cpp
// a.cpp 里面有一个定义
void f() { }

// b.cpp 里面有一个定义
void f() { }

// 每个翻译单元各只有一个 f 的定义，不违背第一条规则
```

注意，`f` 是外部连接的符号。也就是说，链接器会认为 `a.cpp` 和 `b.cpp` 中的 `f` 是同一个 `f`。然后，链接器发现 `f` 在不同的翻译单元分别给出了定义，于是报重定义错误：
```
ld.exe: ...a.o:a.cpp:(.text+0x0): multiple definition of `f()';
    ...b.o:b.cpp:(.text+0x0): first defined here
```

那么如何在保留两个定义的前提下让链接成功呢？有两个办法。其一是把 `f` 改成内部连接，让链接器认为 `a.cpp` 和 `b.cpp` 中的 `f` 分别是两个函数，互不干扰：
```cpp
// a.cpp （可以用 static 修饰...）
static void f() { }
// b.cpp （...也可以用匿名命名空间）
namespace {
void f() { }
}
```

但这样就会导致生成的二进制可执行文件体积变大（因为有两个一模一样的 `f` 函数被塞进去了）。那么第二个解决方案就是让它们变成内联的，我将在下一节展开有关内联的语法。