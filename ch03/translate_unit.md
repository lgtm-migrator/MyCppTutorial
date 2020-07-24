# 翻译单元

学习了函数之后，我们终于可以完整地介绍 C++ 程序的结构了。

## 编译（翻译）过程

首先来了解一下 C++ 程序如何进行编译的。

当我们把 C++ 代码写在一个文件内的时候，这个文件被称为 **C++ 源文件（C++ source file）**。一般情况下，这个文件经过编译后会生成另外一个文件，而这个文件存储了供计算机读取的二进制指令序列，则称这个文件为 **可执行文件（Executable file）**或**可执行程序（Executable program）**。计算机可以直接运行这个可执行文件从而做我们想要做的事情。

C++ 语言本质上定义了一套规则标准，如何从若干个 C++ 源文件转化为一个可执行程序。在一般场合，我们称这个过程为编译，而本节为了防止歧义，称这个过程为**翻译（Translation）**。