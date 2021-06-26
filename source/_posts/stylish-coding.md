---
title: 养成良好的代码习惯，及其重要性
date: 2018-01-18 02:04:44
tags:
---

初入程序猿的世界，多多少少感到有些新奇，迫不及待地想要自己搞个大东西出来。

但隔个十来天再打开的时候：

> 卧槽，我都写了什么？？？

（我突然方了.jpg）

*（以下内容均以 C# 作为演示语言，并主要针对开源项目）*<!-- more -->

## 排版

虽然现代 IDE 或高级文本编辑器一般都有自动缩进等排版纠正功能，但在使用部分简单的文本编辑器修改代码时，请注意以下内容：

### 缩进

1. 注意手动添加缩进。
2. 统一缩进符，比如均使用**制表符**，或均使用 **n 个空格**。

正确操作：

```
using System;
namespace sample {
    public class sample {
        int main(string[] args) {
            ...
        }
    }
}
```

错误操作：

```
using System;
namespace sample{
public class sample{
int main(string[] args){
...
}
}
}
```

### 适当换行、空格

1. 适当换行。
2. 适当加入空格符号。
3. 使用空行来区分同样缩进量的临近代码中处理不同任务 / 事务的代码块。

正确操作：

```
int main(string[] args, bool enableEcho = true) {
    ... // Proceed A.
    ...
    input += 10;
    ...

    ... // Proceed B.

    ... // Returns value.
}
```

错误操作：

```
int main(string[] args,bool enableEcho=true){
    ... // Proceed A.
    ...
    input+=10;
    ...
    ... // Proceed B.
    ... // Returns value.
}
```

### 注释

注释很有用，但使用时也需要注意单行注释、块注释的位置和排版需求。

C# 等高级语言可以使用 XML 注释等与 IDE 代码提示结合的注释方式，提高易读性。

**注释文本中请注意基本文本排版需求。**

正确操作：

```
/// <summary>
/// Main entry of the program.
/// </summary>
/// <param name="args">Input arguments.</param>
/// <param name="enableEcho">Indicates whether the function should output or not.</param>
/// <returns>Process result.</returns>
int main(string[] args, bool enableEcho = true) {
    ... // Proceed A.

    ... // Proceed B.

    ... // Returns value.
}
```

错误操作：

```
// enableEcho:indicates whether the function should output ornot.
int main(string[] args, bool enableEcho = true) {
    ... //Proceed A

    ... //Proceed B

    ... //Returns value
}
```

## 优雅地开发

同样的目的，可以写出很多种不同的代码，但请尽可能选择最优雅、最完美的一个。

同时，在生产环境中也需要考虑到将来代码运行过程中所可能遇到的种种意外情况，包括但不限于：

1. 读取文件前进行权限检查，而不是直接读取。
2. 线程操作前检查线程状态，而不是直接访问。
3. 采用 Thread-Safe 的方式，而不是绕过 Thread-Safe.

## 将一样功能做到极致

做人不能三心二意，写程序也是如此。

开发一个项目时，专心致志地做好它应该做好的功能，而不是零零碎碎地做一个低质量的全家桶。

除非全家桶的每一样的「吃相」都很好，否则倒头来你没有任何方面比其他的更优秀。

## 代码维护简化

考虑到后期代码维护，尤其是开源项目，需要提高可读性及易维护性、组织性，尤其是对于贡献者来说。

~~当然也不排除连自己都不认识自己都写了些啥的情况2333~~

### 尽可能使用能看出设计目的及其存在意义的对象名称

正确操作：

```
int main(string[] args) {
    string welcome = "Welcome!";
    bool useStdErr = false;
    ...
}
```

错误操作：

```
int main(string[] args) {
    string a = "Welcome!";
    bool b = false;
    ...
}
```

### 复杂方法 / 函数 / 过程中，注意添加注释

正确操作：

```
/*
 main function. And also the application's main entry point.
 enableEcho: Indicates whether the function should output or not.
 returns: The operation state, succeeded or failed.
*/
int main(string[] args, bool enableEcho = true) {
    string welcome = "Welcome!"; // The first line of output when application starts.
    bool useStdErr = false;
    ...
}
```

错误操作：同样的代码，但没有任何注释。

### 注意代码分类

处理同一类功能的代码应当放在一个独立的类、命名空间或者文件中。

同时，尽可能避免将功能作用不同甚至毫不相干的代码，放在不同的地方。

正确操作：

```
namespace data {
    public class configurations {
        bool validate() { ... }
        ...
    }
    public class statistics {
        void update() { ... }
        ....
    }
    ...
}
```

错误操作：

```
namespace data {
    public class container {
        bool validate() { ... }
        void update() { ... }
        ...
    }
    ...
}
```

## 遵循社区开发规范

遵循社区开发规范，能有效提高代码可读性和维护性，同时也能提高自己的开发效率。

### 使用有意义的版本号

比如 Windows 上经典的四段式版本号：

`Major.Minor.BuildNumber.Revision`

或者，你也可以自创版本号格式，只要你喜欢。

但务必注明版本号意义：

如 `1.0-rs-b5`：

主版本 1，次要版本 0，是正式发行版（Release），第 5 次编译。

### 文件头部注释

这一点对于开源项目来说尤为重要。

应当注明的内容包括但不限于：

1. 当前应用程序 / 解决方案使用的开源许可证（如果有）
2. 此代码文件的版权信息。
3. 使用到的第三方代码，及其许可证（如果有）
4. 此代码文件所包含代码的用途。

### 合理使用 GitHub

开源，甚至是闭源（专有）软件开发者都难免接触 GitHub。

请注意 GitHub 是一个使用 Git 的代码托管网站，不是（你的）：

- 网盘
- 文件分享网站
- 小本本
- ...

请不要在一个 GitHub Repo. 里放置**无意义的文件**（比如编译输出，日志等）。

## 结语

当然，上述内容中限制很多，但并不代表要扼杀一个人觉得很合适的习惯，毕竟：适合自己的，才是最好的～
