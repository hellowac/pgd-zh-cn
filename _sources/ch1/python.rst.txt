Python
============================================

.. tab:: 中文

    Python (http://python.org) 是一种现代的高级编程语言，适用于各种编程任务。它常用作脚本语言，用于在操作系统层面上自动化和简化任务，但同样也适合构建大型和复杂的程序。Python 已被用于编写基于 Web 的系统、桌面应用程序、游戏、科学编程，甚至是实用工具和各种操作系统的其他高级组件。

    Python 支持多种编程范式，从简单的过程化编程到面向对象编程和函数式编程。

    虽然 Python 通常被认为是一种“解释型”语言，并且偶尔因与像 C 这样的“编译型”语言相比较慢而受到批评，但使用字节编译以及大量工作由库代码完成意味着 Python 的性能通常出奇地好。

    Python 解释器的开源版本可供所有主要操作系统免费使用。Python 非常适合各种编程任务，从快速的一次性脚本到构建庞大而复杂的系统。它甚至可以在交互式（命令行）模式下运行，允许您输入命令并立即看到结果。这对于快速计算或了解某个特定库的工作原理非常理想。

    开发人员在使用 Python 时，首先会注意到与其他语言（如 Java 或 C++）相比，Python 是多么富有表现力：在 Java 中可能需要 20 或 30 行代码的任务，在 Python 中通常只需几行代码。例如，假设您想打印给定文本中出现的单词的排序列表。在 Python 中，这非常简单::

        words = set(text.split())
        for word in sorted(words):
            print(word)

    在其他语言中实现这一任务通常会出乎意料地困难。

    虽然 Python 语言本身使得编程快速而简便，让您可以专注于当前任务，但 Python 标准库使得编程更加高效。这些库使得执行诸如转换日期和时间值、操作字符串、从网站下载数据、进行复杂数学运算、处理电子邮件、编码和解码数据、XML 解析、数据加密、文件操作、压缩和解压文件、与数据库交互等任务变得轻松。您可以通过 Python 标准库做的事情真是令人惊叹。

    除了 Python 标准库中的内置模块外，您还可以轻松下载并安装自定义模块，这些模块可以用 Python 或 C 编写。

    **Python 包索引** (http://pypi.python.org) 提供了成千上万的额外模块，您可以下载和安装。如果这还不够，许多其他系统提供了 Python 的“绑定”，让您可以直接从程序中访问它们。本书中将大量使用 Python 绑定。

    .. note::

        需要指出的是，Python 有不同版本。Python 2.x 是当前最常用的版本，而 Python 开发者过去几年一直在开发一个全新的、不兼容旧版的 Python 版本，称为 Python 3。最终，Python 3 将取代 Python 2.x，但目前大多数第三方库（包括我们将使用的所有 GIS 工具）仅支持 Python 2.x。因此，本书中将不会使用 Python 3。

    Python 在许多方面都是一种理想的编程语言。一旦您熟悉了语言本身并且使用了几次，您会发现编写程序解决各种任务变得异常简单。您不必陷入类型定义和低级字符串操作的困扰，可以专注于实现目标。您最终几乎会直接用 Python 代码思考。用 Python 编程是直接、有效的，而且，敢说一句，*有趣*。

.. tab:: 英文

    Python (http://python.org) is a modern, high level language suitable for a wide variety of programming tasks. It is often used as a scripting language, automating and simplifying tasks at the operating system level, but it is equally suitable for building large and complex programs. Python has been used to write web-based systems, desktop applications, games, scientific programming, and even utilities and other higher-level parts of various operating systems.

    Python supports a wide range of programming idioms, from straightforward procedural programming to object-oriented programming and functional programming.

    While Python is generally considered to be an "interpreted" language, and is occasionally criticized for being slow compared to "compiled" languages such as C, the use of byte-compilation and the fact that much of the heavy lifting is done by library code means that Python's performance is often surprisingly good.

    Open-source versions of the Python interpreter are freely available for all major operating systems. Python is eminently suitable for all sorts of programming, from quick one-off scripts to building huge and complex systems. It can even be run in interactive (command-line) mode, allowing you to type in commands and immediately see the results. This is ideal for doing quick calculations or figuring out how a particular library works.

    One of the first things a developer notices about Python compared with other languages such as Java or C++ is how expressive the language is: what may take 20 or 30 lines of code in Java can often be written in half a dozen lines of code in Python. For example, imagine that you wanted to print a sorted list of the words that occur in a given piece of text. In Python, this is trivial::

        words = set(text.split())
        for word in sorted(words):
        print word

    Implementing this kind of task in other languages is often surprisingly difficult.

    While the Python language itself makes programming quick and easy, allowing you to focus on the task at hand, the Python Standard Libraries make programming even more efficient. These libraries make it easy to do things such as converting date and time values, manipulating strings, downloading data from websites, performing complex maths, working with e-mail messages, encoding and decoding data, XML parsing, data encryption, file manipulation, compressing and decompressing files, working with databases—the list goes on. What you can do with the Python Standard Libraries is truly amazing.

    As well as the built-in modules in the Python Standard Libraries, it is easy to download and install custom modules, which can be written in either Python or C.

    The **Python Package Index** (http://pypi.python.org) provides thousands of additional modules which you can download and install. And if that isn't enough, many other systems provide python "bindings" to allow you to access them directly from within your programs. We will be making heavy use of Python bindings in this book.

    .. note::

        It should be pointed out that there are different versions of Python available. Python 2.x is the most common version in use today, while the Python developers have been working for the past several years on a completely new, non-backwards- compatible version called Python 3. Eventually, Python 3 will replace Python 2.x, but at this stage most of the third-party libraries (including all the GIS tools we will be using) only work with Python 2.x. For this reason, we won't be using Python 3 in this book.

    Python is in many ways an ideal programming language. Once you are familiar with the language itself and have used it a few times, you'll find it incredibly easy to write programs to solve various tasks. Rather than getting buried in a morass of type-definitions and low-level string manipulation, you can simply concentrate on what you want to achieve. You end up almost thinking directly in Python code. Programming in Python is straightforward, efficient, and, dare I say it, *fun*.