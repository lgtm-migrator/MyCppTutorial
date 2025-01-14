# C++ 风格类型转换

在进行之后的讲解之前，容我现在此处插入一些“题外的”内容。我们之前已经了解了 [C 风格类型转换](/ch02/part2/other_operator#类型转换运算符)，而现在我们要讲的是 C++ 风格类型转换。

所谓“C 风格”、“C++ 风格”，从字面意思上就能看出，这只是一种代码风格上的区别。它们除了写法之外，做的事情都是一样的：转换一个操作数的类型。那么，回顾一下之前 C 风格的类型转换，它长成这样：

```cpp
int a{42};
float x{1.0f}, y{2.0f};

(double)a;
int(x + y);
float(5 / 3);
```

而 C++ 风格不太一样，它长成这样：

```cpp
int a{42};
float x{1.0f}, y{2.0f};

static_cast<double>(a);
static_cast<int>(x + y);
static_cast<float>(5 / 3);
```

可以看出，C++ 风格转型都长成 `@啥啥啥"_cast<"类型标识">("操作数")"@` 这个样子（不得不说，比 C 风格要多打不少字）。具体来讲，C++ 风格转型按照转换操作的危险程度分成四种，在形式上提醒程序员在编程时应注意自己代码所包含的风险程度。下面我们将一一介绍它们。

| 运算符                   | 名称                    | 作用                       |
| ------------------------ | ----------------------- | -------------------------- |
| `const_cast<T>(a)`       | const_cast 运算符       | 用于移除指针或引用的只读性 |
| `static_cast<T>(a)`      | static_cast 运算符      | 显式类型转换               |
| `dynamic_cast<T>(a)`     | dynamic_cast 运算符     | 检查对象的动态类型后转换   |
| `reinterpret_cast<T>(a)` | reinterpret_cast 运算符 | 重新解释指针含义           |

## `static_cast`

`static_cast` 是比较安全的一种转换。所谓安全，就是指这个转换导致的行为都能保证合乎预期：比如整型和浮点类型间的转换保证了其值尽可能不变。刚才的三个例子都属于这个范畴，所以它们在改写成 C++ 风格转型后全都是 `static_cast`。具体而言，`static_cast` 容许如下转换：
- 各种隐式转换（主要包括[算术类型间转换](/ch02/part2/implicit_conversion)和[自定义类型转换](/ch06/cast_overload)，还有添加指针只读性（即 `T*` 到 `const T*`）的转换、到 `void*` 的转换等）;
- 显式（`explicit`）的自定义类型转换；
- 枚举类型和整数类型之间的转换；
- 派生类指针到基类指针的转换（“[向上转型](/ch07/inheritance/implicit_cast_in_inheritance#指针转换)”）；
- 基类指针到派生类指针的转换（“[向下转型](/ch07/inheritance/implicit_cast_in_inheritance#反之？)”）；
- 等等。

然而我需要在这里埋下一个伏笔， 尽管称 `static_cast` 是“最安全”的转型，但它仍然不够安全。它的不安全性体现在“向下转型”上。下一节我将展开讲解其为何是不安全的。

## `const_cast`

`const_cast` 用于一些不那么安全的转换——移除只读性的转换。首先来看下面带 C 风格转换的代码：
```cpp codemo(show)
#include <iostream>
int main() {
    const int a{42};
    int* q{(int*)(&a)};
    *q = 56;                     // 会发生什么？
    std::cout << a << std::endl; // 又会输出什么？
}
```

呃，上面的代码是非常不好的，不要学。在第 3 行程序声明并定义了**只读**变量 `a`，但却在第 4 行强行让一个**不带只读性**（即非 `const int*` 而是 `int*`）的指针 `p` 指向它。那么，能否更改 `p` 所指向地址的内容是未定义的——明明刚刚说 `a` 是只读的、不能修改；过后又拐弯抹角地修改它，这让编译器很为难。最终的输出有可能是 `42`，也有可能是 `56`，我们并不知道它确切会发生什么。也就是说，第 4 行的 `(int*)(&a)` 这个转换并不是安全的。所以，`static_cast` **不允许移除指针只读性的转换**。

但是，在一些情形下（通常是面向底层的编程）这种不安全转换又是必需的，那么这时就只能用 `const_cast` 了。改写后的版本如下：

```cpp codemo(show)
int main() {
    const int a{42};
    int* q{const_cast<int*>(&a)};
    *q = 56;                     // 未定义行为
    // [...]
}
```

如果你把上面代码中的 `const_cast` 改成 `static_cast`，编译会出错。

## `reinterpret_cast`

所谓 `reinterpret_cast`，按字面意思就是“重新解释的转换”。它可以完全不考虑安全性地得到一个“不合法”的指针值。比如：
```cpp codemo(show)
int main() {
    int a{42};
    float* f{nullptr};
    f = reinterpret_cast<float*>(&a);
}
```

这里直接让一个 `float*` 类型的指针指向一个 `int` 类型的数据。这已经超乎正常编程的范围了。又比如：
```cpp codemo(show)
int main() {
    long long a{0x601234ll};
    void* p{nullptr};
    p = reinterpret_cast<void*>(a);
}
```
这里直接让 `p` 指向一个自己定义的莫名其妙的地址。在这个例子中，这种转换直接把一个非指针类型的值转换为指针类型，即把一个普通的数转换为地址（反过来也可以）。总之，如果不是为了编写底层程序（偏向操作系统和编译器的程序）的话，一般是用不到它的。

> `reinterpret_cast` 无法移除只读性，故任意的指针转换（即 C 风格转换的等价写法）在 C++ 风格中需要 `reinterpret_cast` 和 `const_cast` 两次转换。

## `dynamic_cast`

正如之前所说，`static_cast` 并不完全安全，而 `dynamic_cast` 就是为了弥补其不足而设计的。下一节我将通过一些更具体的例子来讲解 `dynamic_cast` 的用法。
