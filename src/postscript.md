# 后记

## 作者的话

整个项目的最核心问题只有一个：为什么它要存在？也即说，市面上、网络上那么多 C++ 教程，为什么要自己从零写一本？这个问题我并没有很好的答案。它最开始，只是一个作为课程参考的辅助材料而已；但随着项目的发展，它已经脱离了原本的目标。

### 关于内容的随谈

在前言中，我提到了整本书的基本架构：先是 C 风格的《计概》部分，然后是 OO 风格的《程设》部分，最后是函数式风格及现代 C++。从一个非常宏观的视角来看，整本书在风格上的割裂感是非常严重的；举一个简单的例子，`using namespace std;` 在 C 风格部分几乎是“起手式”一般的存在，但之后我就开始极力避免这句话了。这其中的原因是，我在尽可能地让 C 风格部分的代码和中国大陆高校的传统教学风格（说的就是你，谭浩强）靠近。另外，OO 风格部分的代码也基本依照着从对象，到继承，再到多态和模板的这样一个普通而又常见的顺序来讲。但 OO 风格的代码在之后的章节又见不到了。所以我说，我这个教程并不是一个很成体系的东西。

但我在整份教材里最为坚持的一件事情就是“常维新”。比如，我非常激进地在 C 风格部分引入了统一初始化语法，这似乎与我刚刚说的“贴合教学风格”一言相矛盾。在和别人的讨论中，我总结了 C 风格部分中我最为激进的两个重点：一是刚刚说的统一初始化，二是常量求值（`constexpr`）的概念。我是有一些小心思在里面的：首先，这些语法算是原有语法的**改进**而不是**重构**，因此从新写法入手，转换到旧写法（也就是第四章结尾的“迁移到 C”）是比较平滑的。其次，这些语法能帮助解决后期的大量问题，比如赋值和初始化的区别，比如常量和只读变量的区别。虽然我也可以在后期去重申这些问题，但很不幸的是“数组”这个 C 风格的东西非常强调这两点。幸运的是，在近一年的实践看来，这两处激进没有导致什么恶劣后果。

OO 部分的东西，我开始引入自己的很多思考。在起初编写这一部分时，我的经验和理解说实话确实不够；所以，我的叙事风格和主流教材也有很多区别。最明显的一点就是，我把“运算符重载”这一个在别人教材里属于“小知识点”的内容晋升为一整章的主题。随后，与运算符重载的相关内容被吸纳进去，比如静态成员和友元。而重载之前的内容，则是通过一个贯穿全章的例子来介绍一些基础的，只属于 C++ 的语法。我个人认为，C++ 最特别的设计就在于运算符重载。从历史上来看，正因为有了运算符重载，才有了引用，才有了 ADL，才有了友元，才有了函数对象等等一系列的语法（当然其中某些或许是语法噪声）。另一方面，运算符重载也为整个语言引入了大量的趣味性，这也是我在第六章中开篇点明的。我不清楚这种安排对学生是否有利，因为我并不是《程设》课程的助教。但从一些小的反馈来看还是不错的。

有一些内容我自己也不熟悉，比如多重继承。多重继承自打章节名写下之后，就几乎没有进一步动过笔——因为我不知道怎样写是合理的，我自己没有用过。但传统教材至少会花费一节的笔墨来强调多重继承、“菱形继承”和虚基类这些语法，只不过我没有写过这种代码，而且没怎么见到过。我在前言中也提到过这件事，就是我希望我教的东西都是有用处的，不管是哪一部分的内容我都会去想这件事。之前提到的 C 风格部分的两个激进语法，也有这种程度的考量——我希望学生们多使用新语法。

OO 部分本身的新东西不多。像 `=default` `=delete` `final` `override` 这些，也只是作为细节性的知识点在里面提及了。但在 OO 的结尾——模板里，我将“新东西” CTAD 作为和模板实参推导同等地位的语法来讲。我希望读者在读这些章节时，认为这些语法是“天然”存在的，而不是后期打补丁打出来的。我不喜欢“专门讲新标准”的写法。点名批评 *C++ Primer*（第五版），和郭炜的《新标准 C++ 程序设计》。这两本书都将 C++11 “新”标准放在全书结尾作为高级进阶内容，我认为这样是不对的。新标准的存在，不是 1 + 1 的“加法”，而是 1 = 2 的“赋值”。新的东西应该融洽地与原有标准一起存在。

在函数式部分之前，我插入了两个小章节。

《链接》是国内教材几乎不提及的，《常用库介绍》则是一个大杂烩。引入链接的原因是纯粹实践性的；许多学生不清楚如何多文件编译 C++ 程序——直到大三才知道！我意识到国内的教材仿佛有一个暗暗的指向，就是信息学竞赛和算法竞赛。这些竞赛是不要求任何实践的，它们只用提交一个单文件就够了。但这个世界上更多的代码不是独立存在的，而是作为一整个项目的一部分被链接起来的。我听说北大之后的《程设》课程开始使用 Qt 作为项目框架，或许能帮助他们对这方面有一些了解。

至于《常用库介绍》，则是一个大杂烩。最初，这个章节只是应急用的：有一些本应学习“C 风格部分”的学生需要进行文件系统操作和文件读写，于是我紧急地添加了这样一个章节。但后来我发现，我在开始函数式的介绍之前，还有许多前置的知识（就如同引用必须在 OO 之前介绍一样），这其中就包括著名的智能指针。移动语义这个语法，也只能作为智能指针的报幕了。说到这里，你也会注意到我采用了 C++17 的值语义，绝不同于网上的大部分文章。这也是整本教材的“新”之体现。我在计划中的“错误处理”章节，打算讲 C 风格的错误码、OO 风格的异常、函数式的 `std::expected`（这是 C++23 诶！）等等……总之，我在这一部分会将大量的琐碎内容扔进来。我也知道，这一部分的阅读体验可能很糟糕，但本人真的能力有限，我不知道还能怎样写。

函数式编程这一章是非常糟糕的，糟糕透顶。核心原因是 C++ 离函数式编程语言差着十万八千里，在已有知识体系内讲明白实在太困难了。我这一章的第一个里程碑是 STL 算法。我希望 C++20 引入的、非常棒的基于范围的 STLv2 算法能够推广开来。这些算法写起来是有“函数式味道”的，但不算理论上的函数式。我自认为我比较成功地通过“基础语法”和“STL算法”两个部分把这一点讲明白。但后续——下一个里程碑该如何编写呢？我还不知道。《C++ 函数式编程》这本书里还有大量的内容没有被讲到……

### 关于如何学习

所以，这个教程算不上好。我只能说，整本教材至少在前八章是复合北大信科的教学路线的，并保证内容足够“新”。其它的事情，我没有能力去做。但我仍然希望我的教程能够转换学生的观念：从“源自‘实然’的学习”转换到“源自‘应然’的学习”。

“应然”和“实然”是两个哲学术语，我这里误用了它们。所谓的“应然”，就是语言标准或语言参考，而“实然”则是编译器的汇编结果。谭浩强那个年代的 C/C++ 学习，都是从实然出发的。换而言之，它们是这样学习的：
- 编写一段代码
- 观察编译器的输出
- 摸索到语言的规律
- 循环

这种学习方法是 OK 的，但不好，至少不适用于 C++。大部分编程语言都有多个实现：
- C: GCC/Clang/TCC/ICC
- C++: GCC/Clang/MSVC/ICC
- JavaScript: 浏览器/NodeJS
- Rust: Rustc/GCC
- C#: .NET Framework/Mono
- Java: Oracle/OpenJDK
- Python: CPython/PyPy
- …

这也是语言标准存在的意义：尽可能保证所有的实现表现行为是一致的。基于实然的学习方法，最多只能学到某一个特定编译器的行为，但无法保证最后写出的代码是通用可靠的。而谭浩强写出的代码，在今天就已经不可靠了。这也是为什么我反对基于实然的学习方法。

基于应然的学习，则是从语言标准提炼、简化出的一般规则。我为了编写这本教材，几乎翻遍了 CppReference 上的所有章节，就是为了保证教授的内容不是依赖于特定编译器的，而是标准所允许的。我在文中也经常提起“这是未定义行为”“具体行为是未指明的”“这或许不符合标准”之类的文字来强调这个事实。除了链接章节，我在文中尽可能少地提到汇编代码。毕竟不是所有的 C++ 程序都是编译到汇编的，通过汇编学习是非常没有道理的。

> 有些编程语言只有单一实现（比如 TypeScript），那么这时按照实然来学习或许是可接受的。



## 参考文献

- 谭浩强《C++ 程序设计》
- 郭炜《新标准 C++ 程序设计》
- Scott Meyers, *Effective C++* （第 3 版，侯捷 译）
- Stephen Prata, *C++ Primer Plus* （第 6 版，张海龙 袁国忠 译）
- Bjarne Stroustrup, *The C++ Programming Language* （第 4 版）
- Harold Abelson 等《计算机程序的构造和解释》（第 2 版，裘宗燕 译）
- Ivan Cukic《C++ 函数式编程》（程继洪等 译）
- [cppreference.com](https://zh.cppreference.com)
- [Draft C++ Standard](http://eel.is/c++draft/)
- 若干知乎回答与文章；作者 @d41d8c，@Mick23571（C++ 标准委员会成员）

## 致谢 Acknowledgements

- 北京大学软件研究所副教授　　　　[李　戈](http://sei.pku.edu.cn/~lige/index_zh.html)
- 北京大学基础实验教学研究所讲师　郭　炜
- 北京大学王选计算机研究所副教授　[刘家瑛](http://39.96.165.147/people/liujiaying.html)
- 北京大学 2019 级信息科学技术学院本科生　　张钰麟
- 北京大学 2019 级元培学院本科生　　　　　　潘宏弢
- 北京大学 2019 级信息科学技术学院本科生　　曲思睿
- 北京大学 2019 级信息科学技术学院本科生　　庄敏学
- 北京大学 2019 级信息科学技术学院本科生　　娄宇珂
- 北京大学 2019 级心理与认知科学学院本科生　李柄辉
- 北京大学 2019 级数学学院本科生　　　　　　申峻旭
- 北京大学 2016 级信息科学技术学院本科生　　李　拙

感谢以下同学对本项目的支持：
- 北京大学 2019 级信息科学技术学院本科生　　赵一鸣（陕）
- 北京大学 2021 级元培学院本科生　　　　　　肖茜之
- 北京大学 2021 级信息科学技术学院本科生　　李兆彬
- 北京大学 2020 级信息科学技术学院本科生　　王子昊
- 北京大学 2019 级信息科学技术学院本科生　　郭　易

感谢开源社区提供的以下优秀项目：
- [Vuepress](https://v2.vuepress.vuejs.org) 及各种第三方插件（[列表](/contribution.md#插件)）
- [Vue](https://vuejs.org)
- [CodeMirror](https://codemirror.net/)
- [Compiler Explorer](https://godbolt.org) 的开放 API
- [Raphael.js](http://raphaeljs.com/)
- `diff` `markdown-it` `prismjs` `rxjs` `typescript` 等 JavaScript 库和工具
