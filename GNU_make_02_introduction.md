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

规则解释了哪些文件在什么时间该怎么编译。`make` 在执行这条 `recipe` 时，会使用 `prerequisites` 生成或更新 `target`。规则也可以定义在何时执行一个动作，具体请看第 `4` 章【如何书写规则】，第 `23` 页。

除了规则，`makefile` 还可以包含其他内容，当然，最简单的 `makefile` 确实只需要包含规则就行了。另外，有些规则看起来可能会比上面这个格式更加复杂，但是八九不离十的。 


## 一个简单的 `Makefile`

下面我们来看一个很直白的 makefile，它要 make 的最终目标是一个可执行文件 edit，它依赖于 8 个目标文件，这 8 个目标文件呢，又各自依赖于 8 个 C 源文件和 3 个头文件。

这 3 个头文件分别是 `defs.h`，`command.h` 和 `buffer.h`，其中，所有 C 文件都包含了 `defs.h`，而用于定义编辑命令的文件又包含了 `command.h`，用于底层更改缓存的文件包含了`buffer.h`。
```Makefile
    edit : main.o kbd.o command.o display.o \
            insert.o search.o files.o utils.o
            cc -o edit main.o kbd.o command.o display.o \
                    insert.o search.o files.o utils.o
    main.o : main.c defs.h
            cc -c main.c
    kbd.o : kbd.c defs.h command.h
            cc -c kbd.c
    command.o : command.c defs.h command.h
            cc -c command.c
    display.o : display.c defs.h buffer.h
            cc -c display.c
    insert.o : insert.c defs.h buffer.h
            cc -c insert.c
    search.o : search.c defs.h buffer.h
            cc -c search.c
    files.o : files.c defs.h buffer.h command.h
            cc -c files.c
    utils.o : utils.c defs.h
            cc -c utils.c
    clean :
            rm edit main.o kbd.o command.o display.o \
                    insert.o search.o files.o utils.o
```
这里要提一下上面用到的反斜杠 `\`，它用于将很长的一行分成易于阅读的两行，只是为了阅读方便好看，再没有其他用处，具体请查看第 `3.1.1` 节【分割很长的行】，第 `12` 页。

好，我们来运行这个 `makefile`，只需要输入：`make`，就可以得到 edit 这个可执行文件。再输入：`make clean`，你会发现 `edit` 及其他目标文件都不见了。

我们来仔细看看这个 `makefile`，`target` 有可执行文件 `edit`、目标文件 `main.o`、`kdb.o`等。`prerequisites` 有 `main.c` 和 `defs.h`等，实际上，每个 `.o` 文件即是一个 `target`，同时又是一个 `prerequisite`。`recipe` 有 `cc -c main.c` 和 `cc -c kbd.c` 等。

只要 `prerequisite` 有修改，其 `target` 就需要重新编译或链接。另外，如果 `prerequisite` 是自动生成的，那必须先更新它们。回到例子中，`edit` 依赖于各个目标文件，而目标文件又依赖于其源文件和头文件，拿 `main.o` 来说，它依赖于 `main.c` 和 `defs.h`。

`target` 和 `prerequisite` 组成一行，而底下一行紧跟的是 `recipe`，用来描述如何更新 `target` 文件。`recipe` 前面必跟一个 `tab`（前面提到过，如果改过 `.RECIPEPREFIX` 的值，那就是这个新设定的字符），用于区分不同行的多个 `recipe`。（记住，`make` 并不知道 `recipe` 会怎么运行，完全是由你来定的。`make` 只知道有 `target` 需要更新了，那它就会来执行这个 `recipe`。）

`clean` 并不是一个文件，仅仅是一个动作的名字。通常，`clean` 不会作为任何规则里面的 `prerequisite`，谁会动不动就去删除文件呢，因此，只有你主动调用它的时候，它才会执行。`clean` 本身不会作为 `prerequisite`，那它本身会依赖其他 `prerequisite` 吗？答案是否定的，因此，`clean` 就一个用处，就是执行清除这个动作。这类不涉及文件，只是一个动作的 `target`，我们称之为 `伪目标`。更多请参考第 `4.5` 节【伪目标】，第 `31` 页。而第 `5.5` 节【`Recipes` 中的错误】，第 `54` 页，会讲在执行 `rm` 和其他命令时如何忽略错误。

## 2.3 `make` 如何处理 `Makefile`

默认情况下，`make` 是从第一个 `target` 开始执行的（前面不能带 `.`，除非还带了 `/`），而且这个 `target` 就是最终要生成的目标文件。如果你不想把第一个 `target` 作为终极目标，可以用命令行来重设（请参考第 `9.2` 节【指定终极目标的参数】，第 `109` 页），也可以通过设置 `.DEFAULT_GOAL` 参数的值来更改（第 `6.14` 节【其他特殊参数】，第 `80` 页）。

在上面的例子中，默认的 `make` 终极目标是生成或更新可执行文件 `edit`，因此我们把 `edit` 放到最前面作为第一个 `target`。

当你在命令终端敲入 `make` 时，`make` 程序就会在当前目录中读取 `makefile` 文件，并从第一个 `target` 开始执行。在上面的例子，第一条规则就是重链接 `edit`，但是 `edit` 有很多依赖项，所以在执行这条规则之前，需要先执行这些依赖项的规则。所以我们接着往下看这些依赖项的规则（如 `main.o`），很显然，就是要通过编译 `.c` 和 `.h` 文件来更新 `.o` 文件。`make` 会去检查，`.o` 文件是否存在，`.o` 文件的修改时间是否晚于 `.c` 和 `.h` 文件的修改时间，只要满足上面其中一个条件，就会执行这条规则。

所以你发现了吗？后面这些规则能够执行，是因为他们的 `target` 是终极目标 `edit` 的 `prerequisite`。如果后面这些规则不是前面的依赖项，那么这些规则就不会被执行，除非我们主动告诉 `make` 去执行（也就是通过敲命令，如 `make clean`）。

还是说回到这些依赖项的规则（如 `main.o`），它后面跟着的 `.c` 和 `.h` 文件，它们都是是 .o 的依赖项，按理说 `.c` 和 `.h` 文件也应该有自己的依赖项。在上面例子这个 `makefile` 中，并没有指定 `.c` 和 `.h` 文件的依赖项，因此，`make` 不会对它们做什么。但是，如果使用了像 `Bison` 或者 `Yacc` 这些能自动生成 `C` 的程序， `make` 则会利用它们自己的规则。

`make` 根据 `edit` 的依赖项是否进行了重编译，来决定是否需要对 `edit` 进行重链接。跟前面说的一样，如果 `edit` 不存在，或者 `edit` 文件的修改时间晚于任意一个其依赖性的修改时间，`make` 就会执行重链接。

再看这个例子，如果我们修改了 `insert.c` 文件，然后执行 `make`，`make` 会编译这个文件来更新 `insert.o`，然后链接 `edit`。如果我们修改了 `command.h` 文件，然后执行 `make`，`make` 会重编译 `kbd.o`，`command.o` 和 `files.o`，然后链接 `edit`。

## 2.4 如何用变量简化 `makefile`

在前面的例子中，你会发现，所有的目标文件，我们写了两遍：
```Makefile
        edit : main.o kbd.o command.o display.o \
                insert.o search.o files.o utils.o
                cc -o edit main.o kbd.o command.o display.o \
                        insert.o search.o files.o utils.o
```
这种重复的写法很容易产生错误。因为，如果我们想再新加一个目标文件，很可能会顾此失彼，加了这里，忘了那里。幸好，我们可以使用变量来简化 `makefile`，从而避免这种错误。`变量`允许你在一个地方定义一段字符串，然后在其他地方进行替换使用。（参见第 `6` 章【如何使用`变量`】）

使用 `objects`、`OBJECTS`、`objs`、`OBJS`、`obj` 或 `OBJ` 这些名字的变量来替代所有的目标文件，是 `makefile` 中标准的做法。下面我们使用 `objects` 来实现这个 makefile：
```Makefile
        objects = main.o kbd.o command.o display.o \
                insert.o search.o files.o utils.o
```
`objects` 定义好之后，在后面需要使用所有目标文件列表的地方，就可以用 `$(objects)` 来替代了。下面是替代之后的 `makefile`：
```Makefile
        objects = main.o kbd.o command.o display.o \
                insert.o search.o files.o utils.o
        edit : $(objects)
                cc -o edit $(objects)
        main.o : main.c defs.h
                cc -c main.c
        kbd.o : kbd.c defs.h command.h
                cc -c kbd.c
        command.o : command.c defs.h command.h
                cc -c command.c
        display.o : display.c defs.h buffer.h
                cc -c display.c
        insert.o : insert.c defs.h buffer.h
                cc -c insert.c
        search.o : search.c defs.h buffer.h
                cc -c search.c
        files.o : files.c defs.h buffer.h command.h
                cc -c files.c
        utils.o : utils.c defs.h
                cc -c utils.c
        clean :
                rm edit $(objects)
```

## 2.5 如何让 `make` 自己来推测 `Recipes`

还记得 `recipe` 是啥吗？它描述的就是规则里面要执行的那条动作，譬如将若干目标文件链接成可执行文件，又譬如将 `.c` 和 `.h` 文件编译成 `.o` 目标文件。然而对于 `C` 源码文件来说，并不需要去指定这个动作，因为 `make` 本身就隐含了一条规则：要更新一个 `.o` 文件，就需要使用 `cc -c` 命令来编译对应名字的 `.c` 文件。 例如，它会自己调用 `cc -c main.c -o main.o` 这条 `recipe` 来把 `main.c` 编译成 `main.o`。因此，我们可以省略这些目标文件的 `recipes`。（参见第10章【使用隐含规则】）

更进一步，make 知道是要用`.o` 文件对应名字的 `.c` 文件，它会自动把这个 `.c` 文件加到 `prerequisite` 里。于是，我们又可以省略这个 `.c` 文件了。

下面就是 `recipe` 和 `.c` 文件都省略了之后的 `makefile`：
```Makefile
        objects = main.o kbd.o command.o display.o \
                insert.o search.o files.o utils.o
        edit : $(objects)
                cc -o edit $(objects)
        main.o : defs.h
        kbd.o : defs.h command.h
        command.o : defs.h command.h
        display.o : defs.h buffer.h
        insert.o : defs.h buffer.h
        search.o : defs.h buffer.h
        files.o : defs.h buffer.h command.h
        utils.o : defs.h
        .PHONY : clean
        clean :
                rm edit $(objects)
```

在实际使用中，`makefile` 都是这么写的。（关于 `clean` 相关的内容，我们会在后续其他地方见到，你也可以参考第 `4.5` 节【伪目标】和第 `5.5` 节【`Recipes` 中的错误】。）

看到了吧？隐式规则非常方便，所以它是非常重要的，使用非常频繁。


## 2.6 `Makefile` 的另一种样式
如果 makefile 中所有的目标文件都是由隐式规则创建的，那我们还可以写成另外一种样式。在这种样式中，规则变成了按 `prerequisite` 归类，而前面我们看到的是按 `target` 来归类的。来看看这种样式： 
```Makefile
        objects = main.o kbd.o command.o display.o \
                insert.o search.o files.o utils.o
        edit : $(objects)
                cc -o edit $(objects)
        $(objects) : defs.h
        kbd.o command.o files.o : command.h
        display.o insert.o search.o files.o : buffer.h
```

不难发现，`defs.h` 是作为所有目标文件的 `prerequisite` 的，而 `command.h` 只作为了 `kbd.o command.o files.o` 的 `prerequisite`，`buffer.h` 只作为了 `display.o insert.o search.o files.o` 的 `prerequisite`。

那么问题来了，哪种样式更好呢？只能说萝卜青菜各有所好。这种看起来更紧凑，而有的人则认为按 `target` 归类更加清晰明了。

## 2.7 `clean` 规则

终于要隆重介绍 `clean` 这条规则了，前面基本都是一笔带过。我想你用 `makefile` 不会只想编译一个程序吧？除了编译程序之外，它还可以做很多事情，譬如，删除所有的目标文件和最终的可执行文件，使目录变得干净清爽。前面如果你执行了 `make`，你可以去看看当前目录下的文件，很多是吧？

下面这条规则就是用于删除当前目录下的 `edit` 文件以及所有 `.o` 文件，前面你已经看到过很多次了：
```Makefile
        clean:
                rm edit $(objects)
```
而实际情况中，为了避免出现一些我们意想不到的情况，我们会写成这样：
```Makefile
        .PHONY : clean
        clean:
                -rm edit $(objects)
```
`.PHONY : clean` 把 `clean` 定义为一个`伪目标`，这样就能防止 `make` 去调用一个真实存在的 clean 文件了。而 `rm` 之前的 `-`，则是说遇到错误继续执行。（参见第 `4.5` 节【伪目标】和第 `5.5` 节【`Recipes` 中的错误】。）

我们不会把这类规则放在 `makefile` 的最前面，因为我们不希望它自动执行。别忘了我们前面讲过，放在最前面的第一个规则是默认自动执行的，一般都是我们要生成的最终文件。

前面也提过，要执行 `clean` 规则，你需要敲入命令： `make clean`，不然它永远不会执行。