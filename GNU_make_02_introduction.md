# `Makefiles` 介绍
`makefile` 是个啥，首先，它是一个不带后缀名的文件，其次，它用来告诉 `make` 程序要做什么，更常规地说，`makefile` 用来告诉 `make` 该如何编译和链接一个程序。

本章我们会讨论一个很简单的 `makefile`，它描述了如何将 `8` 个 `C` 源码文件和 `3` 个头文件编译并链接成一个文本编辑器。`makefile` 还能决定 `make` 如何运行各种各样的命令（例如，像一个清除操作那样删除某些文件）。要查看更复杂的 `makefile` 示例，请参见附录 `C`【复杂`Makefile`】，第 `191` 页。


## “规则”长什么样
一个 `makefile` 里包含的规则长这样：
```Makefile
    target ... : prerequisites ...
            recipe
            ...
            ...
```
`target` 通常是要生成的文件的名字，这个 `target` 可以是可执行文件，也可以是目标文件。`target` 还可以是要执行的动作的名称，如 `clean`（参见第 `4.5` 节[伪目标]，第 `31` 页）。
`prerequisite` 是生成 `target` 所需要用到的文件。一个 `target` 通常依赖于几个文件。
`recipe` 是 `make` 要执行的一个动作。一个 `recipe` 可能包含多条命令，要么都写在同一行，要么每行一条。**注意：** 在每个 `recipe` 之前都应该有一个 `Tab`，这是给粗心的人下的陷阱。如果你不喜欢 `Tab`，没问题，挑一个你中意的字符，将 `.RECIPEPREFIX` 变量设置为这个字符即可（参见第 `6.14` 节【特殊变量】，第 `80` 页）。

通常来说，规则中都会带 `prerequisites`，一旦 `prerequisites` 文件有改动，`recipe` 就会重新生成 `target`。但规则中也可以不带 `prerequisites`，例如，`target` 为 `clean` 的这条规则就没有 `prerequisites`，`clean` 是一个伪目标，用来删除文件，后面会详细讲。 

## 一个简单的 `Makefile`