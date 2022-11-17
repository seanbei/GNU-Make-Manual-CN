# `make` 概述
`make` 是个啥？首先，它是一个应用程序，其次，它会自动决定哪些文件需要被重新编译，然后触发相应的命令。本手册介绍 `GNU` 的 `make`，它是由 `Richard Stallman` 和 `Roland McGrath` 开发实现的。`3.76` 之后的版本由 `Paul D. Smith` 负责开发。

`GNU make` 符合 `IEEE` 标准 `1003.2-1992 (POSIX.2)` 第 `6.2` 节。

本手册使用 `C` 编程语言来演示用例，毕竟 `C` 语言是使用最广泛的语言之一。当然，你也可以使用其他编程语言，只要编译器支持 `shell` 命令就行。事实上，`make` 跟编程语言无关，假设你想实现这样一个功能，在一堆文件中，每当其中有一个更新了，其他的都会自动更新，`make` 就可以帮你轻松实现这些。

在使用 `make` 之前，你需要创建一个叫 `makefile` 的文件，用于描述文件之间的关系，以及更新每个文件需要的命令。很典型的例子就是，可执行文件由其目标文件更新，而目标文件又由其源码文件更新。

只要 `makefile` 正确，每次源码文件有改动时，只需敲一个 `shell` 命令：`make`，即可执行所有必要的重编译。`make` 程序会根据 `makefile` 中的数据内容，以及相关文件的最后修改时间，来决定需要更新哪些文件。

你可以给 `make` 提供命令行参数，来控制哪些文件需要重编译，或者如何进行重编译。参加第 `9` 章【如何运行 `make`】，第 `109` 页。

## 如何阅读本手册

如果你对 `make` 一无所知，或只是想了解个大概，那么只需要看每个章节的前半部分即可，不用看后面的部分。因为在每一个章节中，前半部分讲的是一些常规性的信息，后半部分会涉及具体的技术细节。当然有一个例外，第 `2` 章【`Makefiles` 简介】，第 `3` 页，所有的都是介绍性的内容。

如果你熟悉其他版本的 `make` 程序，可以参阅第 `14` 章【`GNU make`的特性】，第 `157` 页，其中列出了 `GNU make` 的增强功能，还有第15章【不兼容和缺失的特性】，第 `161` 页，其中解释了 `GNU make` 相比其他版本缺少的东西。

如果想要快速看一个总结，请参见第 `9.8` 节【选项摘要】，第 `114` 页，附录 `a`【快速参考】，第 `179` 页，第 `4.8` 节【特殊目标】，第 `34` 页。

## 如何提交 `Bug`

使用 `GNU make` 发现了 `bug` 怎么办？快点向开发者报告。我们不承诺任何事，但一不小心，我们给改了呢。

在报 `bug` 之前呢，你可要再三确认它是不是真的 `bug`。怎么做呢，反复仔细地阅读文档手册，看看它里面是不是明确写了可以这么玩儿。如果它没写，或者写的不明白，那好，赶紧报告，这是文档中的 `bug`。

接下来，你可以尝试用最精简的 `makefile` 来复现这个 `bug`，然后把这个 `makefile` 以及运行 `make` 之后完整的结果发给我们，注意，一定是要完整的，包括任何错误和告警信息。千万不要修改这些信息，一定是要复制粘贴过来的。还有，在这个 makefile 中，不要使用那些付费或者非常规的工具，你肯定能模拟出这些工具所对等的 `shell` 命令。最后，你需要描述你期待的 make 结果，这将帮助我们判断这个问题是不是存在于文档中。

问题已经得到精准确认，那么就可以上报啦。要么发邮件到：`bug-make@gnu.org`，要么直接登录 https://savannah.gnu.org/projects/make/ 。除了上面提到的信息，你还需要提供 `make` 的版本号，通过 `make --version` 这个命令就可以获得。还有别忘了你所用的计算机型号及操作系统，怎么查看呢，最快的办法就是运行 `make --help`，在最后几行有你要的东西。

如果你很厉害，想要提交代码更改，请查看 `README` 中 `Submitting Patches` 那一节。