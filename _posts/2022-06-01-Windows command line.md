---
layout: post
title: "Windows command line"
date: "2022-06-01"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Windows command line
---


# Windows Command Line - Study Guide
Windows 命令行 (cmd.exe) 是 Microsoft 等效的 Linux Bash shell。它通常的位置是`C:\Windows\system32\cmd.exe`。

与 Bash 不同的是，windows 命令行主要依赖于内置功能——命令。
其中一些是 `dir`、`cls`、`move` 或 `del`。

由于 Microsoft 将大多数命令行实用程序移至 PowerShell，我们将简要介绍 cmd.exe 功能。您可以在[此处](https://blog.brainasoft.com/all-internal-commands-of-cmd/)找到有关 Windows 命令行命令的更多信息。

# Windows Environment
与 Bash 类似，Windows 也有一个环境；但是，它通常通过 Windows GUI 进行管理。

要在 Windows 10 上管理 Windows 环境变量，您应该进入控制面板 > 系统和安全 > 系统 > 高级系统设置。

通过单击环境变量，您可以管理它们的设置。每当您启动 cmd.exe 时，它​​的环境设置都会从这里拉取。请注意，您可以全局管理变量（系统变量 - 适用于所有用户）或当前用户。

最有趣的变量之一是 Windows PATH 变量。它的工作原理与 Linux 的 PATH 变量完全一样，提供搜索命令行程序的位置。在 Windows 中，可执行目录通过 `;` 分隔。

尝试在命令提示符中输入：`path`

当您尝试在 **cmd.exe** 窗口中执行某些操作时，Windows 系统会检查它是否与任何**内置命令**匹配。如果您尝试执行的不是内置命令，Windows 将在 **PATH** 变量中指定的所有位置中搜索以找到它。

就像在 Bash 中一样，您可以通过向命令行提供相对或绝对位置来调用任何其他程序。您只需要记住，目录斜线指向的方向与 Bash 使用的方向不同。在 bash 中，我们使用正斜杠 `/` ，而在 Windows 中使用反斜杠 `\`。

`..\Windows\notepad.exe`

## Windows Commands and Programs
如前所述，Windows 命令行支持比 Linux 更多的内置命令。话虽如此，一些常见的 Windows 实用程序是位于 C:\Windows\system32 中的独立可执行文件（默认情况下位于 PATH 中）
一个示例是 ping.exe 实用程序。

如果您想从命令行使新安装的软件可执行，则应将其放置在任何 PATH 位置或更改 PATH 变量以包含其位置。请注意，只有少数 Windows 程序支持命令行界面，因此从那里执行它们可能会导致启动软件的 GUI。
例如，您可以尝试在 cmd.exe 窗口中键入“notepad.exe”。

## Windows Output Redirection and Special Characters
Windows 命令行是一个不如 Bash 灵活的脚本环境。

为了访问 Windows 的环境变量，您可以使用 `%variablename%` 来引用它们。您可能还记得，这些变量是在“高级系统设置 - 环境变量”中设置的。要在命令行窗口中打印它们，您需要使用 `echo` 命令。

在您自己的命令行中自己尝试以下操作：

```cmd
echo %PATH%
echo %username%
```

如果您想查看另一个变量，可以使用`SET`命令来完成。此命令可能会生成大量输出，并为您提供各种系统信息。

```cmd
set
ALLUSERSPROFILE=C:\ProgramData
```
您还可以创建自己的**变量**或临时修改现有变量。
任何修改都不会是永久性的，只会存在于**当前的 cmd.exe 窗口**中。
**如果您打开了另一个命令行窗口，则更改其中一个中的变量不会影响另一个。**

输出重定向也适用于 Windows。为了将命令的输出重定向到文件，您可以：
* 使用 `echo aaa > file.txt` 格式创建包含命令输出的文件或使用命令输出**覆盖**现有文件。
* 使用 `echo bbb >> file.txt` 创建包含命令输出的文件或将命令输出**附加**到现有文件。
然后，您可以使用`type`命令查看 file.txt 的内容。

```cmd
echo aaa > file.txt

type file.txt
aaa

echo bbb >> file.txt

type file.txt
aaa
bbb
```

如果您想在 Windows 命令行中执行后续命令，可以使用以下符号来实现：

`command1 & command2` - 无论结果如何都执行

`command1 && command2` - 执行第一个命令，如果成功，执行第二个。

如果您想在 Windows 命令行中执行后续命令，可以使用以下符号来实现：

`command1 | command2` - 将第一个命令的输出发送到第二个命令

`command1 || command2` - 执行第一个命令，如果失败，执行第二个。

# Windows Conditional Statements and Loops
为了创建更大的命令行脚本，您可以将它们保存为 `.bat` 文件，每行一条指令。
Windows 会自动识别此格式并跟随用户执行此类文件。 您可以使用记事本等简单的文本编辑器对其进行编辑。

## Windows Conditional Statements
与 Bash 一样，命令行在比较运算符上也有多种可能性。 但是，在本课程的上下文中，我们将只介绍简单的值比较。
如果您想了解更多关于命令行条件语句的信息，请参考以下链接：

https://ss64.com/nt/if.html

https://ss64.com/nt/else.html

对于比较值，例如变量是否包含某个单词，您可以使用以下说明：

```bat
SET x=qwe

if %x%==qwe (echo true)
true

if %x%==xyz (echo true)

if %x%==xyz (echo true) else (echo "does not contain xyz")
"does not contain xyz"
```
## Windows Loops
Windows 命令行提供具有各种功能的基本 `FOR` 循环。 在本课程的上下文中，我们将介绍使用 `FOR` 和 `FOR /F` 对文件和文件内容进行迭代。
还有一些 FOR 循环开关可用，如果您想探索它们，请单击[此处](https://ss64.com/nt/for.html)。


例如，如果您想使用 for 循环列出目录中的文件，可以使用以下命令：
`for %i in (*.*) do @echo FILE: %i`

命令前的`@`表示隐藏命令提示符（如 `C:\Users>`）并仅显示输出。

```bat
for %i in (*.*) do @echo FILE: %i
FILE: .git-for-windows-updater
FILE: .gitconfig
```
## Reference
[Windows command line reference](https://ss64.com/nt/)

[Else - Windows CMD](https://ss64.com/nt/else.html)

[For - Looping Commands - Windows CMS](https://ss64.com/nt/for.html)

[If - Conditionally Perform Command - Windows CMD](https://ss64.com/nt/if.html)

[Bash programming reference](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html)