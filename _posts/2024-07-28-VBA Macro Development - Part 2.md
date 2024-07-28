---
layout: post
title: "VBA Macro Development - Part 2"
date: "2024-07-28"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# VBA Macro Development - Part 2
Developer -> Macros -> Macros in: Document1 (document)

Save as type: `Word 97-2003 Document`


When you want to document to execute the macros, you need to create subroutines to do that. There are some predefined subroutines that can allow us to do this.

`Document_Open()` allows you to specify what subroutine you want to run when the document opens.


```VB
Sub Document_Open()
    PoC
End Sub

Sub AutoOpen()
    PoC
End Sub
  
Sub PoC()
    Dim payload As String
    payload = "calc.exe"
    CreateObject("Wscript.Shell").Run payload, 0

End Sub
```

这段代码是一个典型的宏病毒示例，它使用了Visual Basic for Applications（VBA）语言编写，通常嵌入在Microsoft Word文档中。代码的目的是在打开文档时自动执行恶意操作。具体来说：

1. **`Document_Open()` 和 `AutoOpen()` 方法**：这两个子程序是宏的自动触发点。`Document_Open()` 方法在文档打开时自动调用，而 `AutoOpen()` 方法在较老版本的Word中用于打开文档时自动执行。这两个方法都调用了 `PoC` 子程序。

2. **`PoC()` 方法**：
   - **变量声明**：这里声明了一个名为 `payload` 的变量，用来存储要执行的命令或程序名。
   - **赋值**：`payload` 被赋值为 `"calc.exe"`，即Windows系统中的计算器程序。
   - **执行**：使用 `CreateObject("Wscript.Shell").Run payload, 0` 命令，这行代码通过Windows脚本宿主（Wscript.Shell）来运行 `payload` 变量中指定的程序（这里是计算器）。这种方法通常用于执行更加恶意的代码或程序。参数 `0` 表示程序在后台运行，用户界面不可见。

这种宏通常用于恶意目的，如启动未经授权的程序、下载并执行恶意软件等。在实际应用中，`"calc.exe"` 可能会被替换为更加恶意的程序路径或命令。在处理此类文档时，建议总是保持警惕，确保宏安全设置得当，避免运行未知来源的宏代码。

The provided code is a typical example of a macro virus, written in Visual Basic for Applications (VBA) and typically embedded in a Microsoft Word document. The purpose of the code is to automatically execute malicious actions when the document is opened. Here are the details:

1. **`Document_Open()` and `AutoOpen()` Subroutines**: These two subroutines are the automatic triggers for the macro. The `Document_Open()` method is automatically called when the document is opened, while the `AutoOpen()` method is used in older versions of Word to execute automatically when a document is opened. Both methods call the `PoC` subroutine.

2. **`PoC()` Subroutine**:
   - **Variable Declaration**: Here, a variable named `payload` is declared, which is used to store the command or program name to be executed.
   - **Assignment**: `payload` is assigned the value `"calc.exe"`, which is the calculator program in Windows systems.
   - **Execution**: The command `CreateObject("Wscript.Shell").Run payload, 0` is used to execute the program specified in the `payload` variable (here, the calculator) via Windows Script Host (`Wscript.Shell`). This method is often used to execute more malicious code or programs. The parameter `0` indicates that the program runs in the background, with no visible user interface.

Such macros are often used for malicious purposes, such as launching unauthorized programs or downloading and executing malware. In practice, `"calc.exe"` could be replaced with a more malicious program path or command. When dealing with such documents, it is advisable to always be vigilant, ensure proper macro security settings, and avoid running macros from unknown sources.

经测试，`AutoOpen()`可以在`Word 97-2003 Document (*.doc)`中运行。

```VB
Sub AutoOpen()
    PoC
End Sub

Sub PoC()
    Dim payload As String
    payload = "calc.exe"
    CreateObject("Wscript.Shell").Run payload, 0

End Sub
```

而`Document_Open()`在在`Word 97-2003 Document (*.doc)`不可运行。

经测试`AutoOpen()`在`Word Macro-Enabled Document (*.docm)`中可以运行。`Document_Open()`在`Word Macro-Enabled Document (*.docm)`不可运行。

`AutoOpen()` is an event that is triggered when a document is open. This event can be used to perform actions automatically when a document is open. 

`AutoOpen()` is triggered when the document is open, and allows us to specify what we want done when a document is opened. 

`Document_Open()`: This is specific to Microsoft Word. There's also open workbook. And is triggered when a document is opened. It's very similar to `AutoOpen()`.

It's recommended that you add both. The reason for that is down to compatibility.

`AutoOpen()` is sort of a legacy event procedure or a function that is triggered in older versions of Word, pre 97 and is retained for backwards compatibility. So if your document needs to support older versions of Word, prior to 97, you want to.

So we keep both of them in the event someone is using older version.

`Document_Open()` is a newer event procedure that's specific to Word 97 and later.

```VB
Sub Document_Open()
    PoC
End Sub


Sub AutoOpen()
    PoC
End Sub


Sub PoC()
    Dim payload As String
    payload = "notepad.exe"
    CreateObject("Wscript.Shell").Run payload, 3
End Sub
```

这段代码同样是使用Visual Basic for Applications（VBA）编写的，用于Microsoft Word文档中的宏。其功能是在打开文档时自动执行特定的程序。这里的代码结构和之前的例子相似，但有一些细节上的差异：

1. **`Document_Open()` 和 `AutoOpen()` 方法**：
   - 这两个子程序用于自动触发宏。`Document_Open()` 在文档被打开时调用，而 `AutoOpen()` 在较旧版本的Word中用于文档打开时自动执行。这两个方法都会调用 `PoC` 子程序。

2. **`PoC()` 方法**：
   - **变量声明**：声明了一个名为 `payload` 的字符串变量。
   - **赋值**：将 `payload` 变量赋值为 `"notepad.exe"`，即Windows系统中的记事本程序。
   - **执行**：使用 `CreateObject("Wscript.Shell").Run payload, 3` 命令。这里，`CreateObject("Wscript.Shell")` 利用Windows脚本宿主来运行 `payload` 变量中指定的程序。参数 `3` 表示程序以“最大化窗口”模式运行。

这种类型的宏可以用于启动系统程序或其他应用程序，但同样也可以被用于恶意目的，比如运行恶意软件。尽管此例中启动的是无害的记事本程序，类似的方法可能用于更危险的行为。在处理来自不确定来源的文档时，应保持警觉并适当设置宏的安全选项。

The provided code, written in Visual Basic for Applications (VBA), is designed for use in a Microsoft Word document macro. Its purpose is to automatically execute a specific program when the document is opened. The structure of this code is similar to the previous example, but with some variations in details:

1. **`Document_Open()` and `AutoOpen()` Subroutines**:
   - These two subroutines serve as the automatic triggers for the macro. `Document_Open()` is called when the document is opened, while `AutoOpen()` is used in older versions of Word to execute automatically upon document opening. Both methods invoke the `PoC` subroutine.

2. **`PoC()` Subroutine**:
   - **Variable Declaration**: A string variable named `payload` is declared.
   - **Assignment**: The `payload` variable is assigned the value `"notepad.exe"`, which is the Notepad program in Windows systems.
   - **Execution**: The command `CreateObject("Wscript.Shell").Run payload, 3` is used. Here, `CreateObject("Wscript.Shell")` utilizes the Windows Script Host to execute the program specified in the `payload` variable. The parameter `3` indicates that the program will run in a "maximized window" mode.

This type of macro can be used to launch system programs or other applications but can also be adapted for malicious purposes, such as running malware. Although in this example, the harmless Notepad program is launched, similar methods could be employed for more hazardous actions. Caution and appropriate macro security settings are recommended when dealing with documents from uncertain sources.

The reason why I don't recommend using the `Word Macro-Enabled Document (*.docm)` is because that is docm format. And the macro enabled document format equates to a document with macros enabled. It's telling the user, this document is used to store macros.

97 onwards, I would recommend keeping both of those. I would recommend keeping `Document_Open()` event and `AutoOpen()` event procedures they're referred to as within your macros.

整个过程：

Word -> Blank document -> Developer -> Macros -> Macros in: Document1 (document) -> Macro name: calc

`1`: it'll activate and display the window. And if the window is minimized of maximized, it'll restore to its original size and position.

File -> Save As -> Save as type: Word 97-2003 Document, Save as type: Word Macro-Enabled Document

Right click -> Properties -> Type of file: Microsoft Word Macro-Enabled Document (.docm), Microsoft Word 97 - 2003 Document (.doc)

The basic premise here that I'm trying to pass along is if we can execute calc.exe, what can't we execute? I'm assuming you can imagine the extent that we can push this.

```VB
Sub Document_Open()
    calc
End Sub

Sub AutoOpen()
    calc
End Sub

Sub calc()
    Dim payload As String
    payload = "calc.exe"
    CreateObject("Wscript.Shell").Run payload, 1
End Sub
```

这段代码是使用Visual Basic for Applications（VBA）编写的，通常用于Microsoft Word文档中的宏。其目的是在文档打开时自动执行计算器程序。详细解释如下：

1. **`Document_Open()` 和 `AutoOpen()` 方法**：
   - 这两个子程序是宏的触发点。`Document_Open()` 在文档打开时自动调用，而 `AutoOpen()` 适用于一些旧版本的Word，在文档打开时也会自动执行。这两个方法都会调用 `calc` 子程序。

2. **`calc()` 方法**：
   - **变量声明**：声明了一个名为 `payload` 的字符串变量，用于存储要执行的程序名称。
   - **赋值**：`payload` 被赋值为 `"calc.exe"`，即Windows系统中的计算器程序。
   - **执行**：通过 `CreateObject("Wscript.Shell").Run payload, 1` 命令执行程序。`CreateObject("Wscript.Shell")` 利用Windows脚本宿主来运行 `payload` 中指定的程序。参数 `1` 表示程序以“普通窗口”模式运行，用户界面可见。

这段宏代码虽然看似简单且无害（仅启动计算器），但同样的技术可以被用来执行更恶意的操作，例如运行未授权的软件或恶意程序。因此，在处理未知来源的宏时，应保持警惕并正确设置宏的安全选项。

This code is written in Visual Basic for Applications (VBA), typically used in Microsoft Word document macros. Its purpose is to automatically execute the calculator program when the document is opened. Here's a detailed explanation:

1. **`Document_Open()` and `AutoOpen()` Subroutines**:
   - These two subroutines serve as the trigger points for the macro. `Document_Open()` is automatically invoked when the document is opened, while `AutoOpen()` is applicable to some older versions of Word, also executing automatically upon document opening. Both methods call the `calc` subroutine.

2. **`calc()` Subroutine**:
   - **Variable Declaration**: A string variable named `payload` is declared to store the name of the program to be executed.
   - **Assignment**: `payload` is assigned the value `"calc.exe"`, which is the Calculator program in Windows systems.
   - **Execution**: The program is executed using the command `CreateObject("Wscript.Shell").Run payload, 1`. `CreateObject("Wscript.Shell")` uses the Windows Script Host to run the program specified in the `payload` variable. The parameter `1` indicates that the program will run in a "normal window" mode, with the user interface visible.

Although this macro code appears simple and harmless (merely launching the calculator), the same techniques can be used to perform more malicious actions, such as running unauthorized software or malware. Therefore, caution and proper macro security settings are recommended when dealing with macros from unknown sources.

What if we wanted to read a registry key?

经测试`AutoOpen()`可以在`Microsoft Word Macro-Enabled Document (.docm)`,和 `Microsoft Word 97 - 2003 Document (.doc)`中运行。

经测试`Document_Open()`在`Microsoft Word Macro-Enabled Document (.docm)`,和 `Microsoft Word 97 - 2003 Document (.doc)`中都无法运行。

```VB
Sub Document_Open()
    reg
End Sub

Sub AutoOpen()
    reg
End Sub

Sub reg()
    Dim wsh As Object
    Set wsh = CreateObject("WScript.Shell")
    
    Dim regKey As String
    regKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion"
    MsgBox "Product Name: " & wsh.RegRead(regKey & "\ProductName")

End Sub
```

这段代码同样使用了Visual Basic for Applications (VBA)，通常用于Microsoft Word文档中的宏。其目的是在文档打开时自动读取并显示Windows系统的产品名称。具体的执行流程如下：

1. **`Document_Open()` 和 `AutoOpen()` 方法**：
   - 这两个子程序作为宏的自动触发点。在文档被打开时或在旧版本的Word中打开文档时，这些方法会自动执行。它们都调用了 `reg` 子程序。

2. **`reg()` 方法**：
   - **对象创建**：首先创建了一个名为 `wsh` 的对象，通过 `CreateObject("WScript.Shell")` 来实例化Windows脚本宿主的Shell对象。这个对象允许我们访问和操作系统级别的功能，如读取注册表。
   - **变量声明和赋值**：声明了一个名为 `regKey` 的字符串变量，并将其赋值为指向Windows注册表中存储当前操作系统版本信息的键的路径："HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion"。
   - **读取注册表和显示信息**：通过 `wsh.RegRead(regKey & "\ProductName")` 读取产品名称的注册表值，并通过 `MsgBox` 弹出一个消息框显示“Product Name: ”后跟产品名称。

这段代码用于读取系统信息，尽管这个特定的示例看似无害（只显示产品名称），但通过类似的方式，宏可以用来执行更复杂和潜在的恶意操作，如修改注册表项或执行其他系统命令。因此，处理来自不确定来源的文档时，保持警惕并适当设置宏的安全选项是非常重要的。

This code is written in Visual Basic for Applications (VBA), typically used within Microsoft Word document macros. Its purpose is to automatically read and display the Windows system product name when the document is opened. Here is the specific execution flow:

1. **`Document_Open()` and `AutoOpen()` Subroutines**:
   - These two subroutines serve as the automatic triggers for the macro. They are invoked when the document is opened or in older versions of Word upon document opening. Both methods call the `reg` subroutine.

2. **`reg()` Subroutine**:
   - **Object Creation**: Initially, an object named `wsh` is created by instantiating the Windows Script Host's Shell object via `CreateObject("WScript.Shell")`. This object allows access to and manipulation of system-level functions, such as reading the registry.
   - **Variable Declaration and Assignment**: A string variable named `regKey` is declared and assigned the path to a registry key where the current operating system version information is stored: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion".
   - **Reading Registry and Displaying Information**: The product name is retrieved from the registry using `wsh.RegRead(regKey & "\ProductName")`, and a message box is displayed showing "Product Name: " followed by the product name.

This code is used to read system information, and while this specific example appears harmless (only displaying the product name), macros can be utilized to perform more complex and potentially malicious operations, such as modifying registry entries or executing other system commands. Therefore, it is crucial to remain vigilant and ensure proper macro security settings when dealing with documents from unknown sources.




