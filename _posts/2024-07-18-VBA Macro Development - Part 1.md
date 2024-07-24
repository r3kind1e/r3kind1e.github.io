---
layout: post
title: "VBA Macro Development - Part 1"
date: "2024-07-18"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# VBA Macro Development
# Demo: VBA Macro Development
`Options` -> `Customize Ribbon` -> make sure that you have checked the `Developer` option or checkbox here to add it to your ribbon

![option-customizeribbon-developer.png](/img/in-post/ine/option-customizeribbon-developer.png)


`Trust Center` -> `Macro Setting`

`Macro Setting`: Disable all macros with notification

Developer Macron Setting: Trust access to the VBA project object model

![trust-center.png](/img/in-post/ine/trust-center.png)

`Developer` -> `Macros` -> Macros in: `Document1 (document)`

When you're creating macros, you want to link it to the document you're working on. It only generates the macros for that document. Otherwise, you'll be generating it for all in the case of the defaults for `All active templates and documents`.

![macrosin-document1.png](/img/in-post/ine/macrosin-document1.png)

![VBS-tools-options.png](/img/in-post/ine/VBS-tools-options.png)

Using the message box

```VB
Sub HelloWorld()

    MsgBox ("Hello World!")

End Sub
```

这段代码是用Visual Basic for Applications (VBA) 编写的，通常用于Microsoft Office应用程序（如Excel、Word等）中。这是一个简单的宏，其作用是显示一个包含消息 "Hello World!" 的对话框。下面是这段代码的详细解释：

1. `Sub HelloWorld()`：这行定义了一个名为 `HelloWorld` 的过程（Sub）。`Sub` 是 VBA 中用来定义一个宏或子过程的关键字。

2. `MsgBox ("Hello World!")`：这行代码调用了 `MsgBox` 函数，显示一个消息框。括号内的字符串 `"Hello World!"` 是要在消息框中显示的文本。

3. `End Sub`：这行代码标记了 `Sub` 过程的结束。

当运行这个宏时，系统会弹出一个对话框，显示文本 "Hello World!"。这种类型的程序通常用于教学初学者如何编写代码和理解基本的编程概念。

The code you've provided is written in Visual Basic for Applications (VBA), commonly used within Microsoft Office applications like Excel and Word. It's a simple macro designed to display a message box with the message "Hello World!" Here's a detailed explanation of the code:

1. `Sub HelloWorld()`: This line defines a procedure named `HelloWorld`. `Sub` is a keyword in VBA used to define a macro or subroutine.

2. `MsgBox ("Hello World!")`: This line of code calls the `MsgBox` function, which displays a message box. The string `"Hello World!"` inside the parentheses is the text displayed in the message box.

3. `End Sub`: This line marks the end of the `Sub` procedure.

When this macro is run, it will pop up a dialog box displaying the text "Hello World!" This type of program is often used to teach beginners how to write code and understand basic programming concepts.

```VB
Sub HelloWorld()

    MsgBox "Hello, World!", vbInformation, "Message Box Demo"

End Sub
```

这段代码也是用Visual Basic for Applications (VBA) 编写的。它是一个宏，用于在Microsoft Office应用程序中显示一个消息框。这个消息框比上一个例子更复杂一些，因为它包括了更多的参数来定制消息框的外观和行为。下面是代码的逐行解释：

1. `Sub HelloWorld()`：这行定义了一个名为 `HelloWorld` 的过程。在VBA中，`Sub` 是定义子程序的关键字。

2. `MsgBox "Hello, World!", vbInformation, "Message Box Demo"`：这行代码使用 `MsgBox` 函数显示一个消息框。
   - `"Hello, World!"` 是消息框中显示的文本。
   - `vbInformation` 是一个常量，用于指定消息框应该显示的图标类型，在这个例子中是一个信息图标。
   - `"Message Box Demo"` 是消息框的标题。

3. `End Sub`：这行代码标记了子程序的结束。

当运行这个宏时，它会弹出一个带有信息图标和自定义标题 "Message Box Demo" 的消息框，显示文本 "Hello, World!"。这样的消息框通常用于提供程序状态的信息，或者在用户界面中显示提示信息。

The code you've shared is written in Visual Basic for Applications (VBA) and defines a macro to display a message box in Microsoft Office applications. This message box is a bit more sophisticated than the previous example as it includes additional parameters to customize the appearance and behavior of the box. Here's a line-by-line explanation:

1. `Sub HelloWorld()`: This line defines a procedure named `HelloWorld`. In VBA, `Sub` is the keyword used to define a subroutine.

2. `MsgBox "Hello, World!", vbInformation, "Message Box Demo"`: This line of code uses the `MsgBox` function to display a message box.
   - `"Hello, World!"` is the text displayed inside the message box.
   - `vbInformation` is a constant that specifies the type of icon to display in the message box; in this case, it's an information icon.
   - `"Message Box Demo"` is the title of the message box.

3. `End Sub`: This line marks the end of the subroutine.

When this macro is executed, it will pop up a message box with an information icon and a custom title "Message Box Demo," displaying the text "Hello, World!" Such message boxes are often used to provide information about the program's status or display hints in a user interface.

![excel-option-developer.png](/img/in-post/ine/excel-option-developer.png)

![office-trust-center.png](/img/in-post/ine/office-trust-center.png)

I wanted to create a new sheet, 

`Developer` -> `Insert` -> `Button` -> make sure to specify the macro linked to this particular book `Macros in: Book1`

![excel-insert-button.png](/img/in-post/ine/excel-insert-button.png)

![assigned-macro-book1.png](/img/in-post/ine/assigned-macro-book1.png)


```VB
Sub Button6_Click()
    MsgBox "Report Generated"
End Sub
```

Right click on the button -> `Assign Macro` -> `Edit`

Make the button create a new worksheet in this workbook.

```VB
Sub Button6_Click()
    MsgBox "Tracker Generated in New Sheet"
    Dim ws As Worksheet
    Set ws = ThisWorkbook.Sheets.Add
    ws.Name = "RedTeamTracker"
    
End Sub
```

这段代码是用Visual Basic for Applications (VBA) 编写的，它在Microsoft Office的Excel应用程序中定义了一个按钮点击事件的宏。这个宏的作用是在当前工作簿中添加一个新的工作表，并给这个新工作表命名。下面是对这段代码的详细解释：

1. `Sub Button6_Click()`：这行定义了一个名为 `Button6_Click` 的过程。这通常是一个按钮控件的事件处理程序，当按钮6被点击时触发。

2. `MsgBox "Tracker Generated in New Sheet"`：这行代码使用 `MsgBox` 函数来显示一个消息框，内容是 "Tracker Generated in New Sheet"。这用来通知用户一个新的追踪器已经在一个新工作表中生成。

3. `Dim ws As Worksheet`：这行代码声明了一个名为 `ws` 的变量，类型为 `Worksheet`。这意味着 `ws` 变量将被用来引用一个Excel工作表对象。

4. `Set ws = ThisWorkbook.Sheets.Add`：这行代码使用 `Sheets.Add` 方法在当前工作簿中添加一个新的工作表，并将这个新工作表的引用赋值给 `ws` 变量。

5. `ws.Name = "RedTeamTracker"`：这行代码设置新添加的工作表的名字为 "RedTeamTracker"。

6. `End Sub`：这行代码标记了子程序的结束。

总的来说，当按钮6被点击时，这个宏会在Excel工作簿中添加一个名为 "RedTeamTracker" 的新工作表，并通过消息框通知用户追踪器已经生成。这对于创建和管理数据跟踪表特别有用。

The code you've provided is written in Visual Basic for Applications (VBA) and defines a macro linked to a button click event in Microsoft Excel. This macro performs actions within the Excel workbook when the button (presumably named "Button6") is clicked. Here's a detailed explanation of the code:

1. `Sub Button6_Click()`: This line defines a procedure named `Button6_Click`. It's typically an event handler for a button control, triggered when button 6 is clicked.

2. `MsgBox "Tracker Generated in New Sheet"`: This line uses the `MsgBox` function to display a message box with the text "Tracker Generated in New Sheet." It notifies the user that a new tracker has been generated in a new worksheet.

3. `Dim ws As Worksheet`: This line declares a variable named `ws` of type `Worksheet`. This means the `ws` variable will be used to reference an Excel worksheet object.

4. `Set ws = ThisWorkbook.Sheets.Add`: This line adds a new worksheet to the current workbook using the `Sheets.Add` method and assigns the reference of the new worksheet to the `ws` variable.

5. `ws.Name = "RedTeamTracker"`: This line sets the name of the newly added worksheet to "RedTeamTracker."

6. `End Sub`: This line marks the end of the subroutine.

In summary, when button 6 is clicked, this macro adds a new worksheet named "RedTeamTracker" to the Excel workbook and notifies the user through a message box that the tracker has been generated. This is particularly useful for creating and managing data tracking sheets.

Use the Windows scripting host or Wscript to run an external program?

`Developer` -> `Macros` -> Macros in: `Document1 (document)`

```VB
Sub Poc()
    Dim payload As String
    payload = "calc.exe"
    CreateObject("Wscript.Shell").Run payload, 0

End Sub
```

这段代码用Visual Basic for Applications (VBA) 编写，功能是在Microsoft Office应用程序中执行一个宏，其主要操作是运行计算器程序。下面是这段代码的中文解释：

1. `Sub Poc()`：定义了一个名为 `Poc` 的过程。在VBA中，`Sub` 是定义子程序的关键字。

2. `Dim payload As String`：声明一个名为 `payload` 的变量，数据类型为 `String`（字符串）。

3. `payload = "calc.exe"`：将字符串 "calc.exe" 赋值给变量 `payload`。这个字符串是Windows操作系统中计算器程序的可执行文件名。

4. `CreateObject("Wscript.Shell").Run payload, 0`：创建一个 `Wscript.Shell` 对象，然后使用该对象的 `Run` 方法来执行 `payload` 变量中指定的程序。这里的 `0` 参数表示程序窗口将隐藏运行。

5. `End Sub`：标记子程序的结束。

总的来说，当执行这个宏时，它会在背景中启动Windows计算器程序，用户看不到任何窗口或界面。这种类型的宏在自动化任务中可能会用到，但也要小心，因为它可以被用于执行恶意代码。

This code is written in Visual Basic for Applications (VBA) and defines a macro designed to execute a specific application within Microsoft Office programs. Here's an explanation of the code in English:

1. `Sub Poc()`: This line defines a procedure named `Poc`. In VBA, `Sub` is the keyword used to define a subroutine.

2. `Dim payload As String`: This line declares a variable named `payload` with the data type `String`.

3. `payload = "calc.exe"`: This line assigns the string "calc.exe" to the variable `payload`. This string is the filename of the Calculator program executable in Windows.

4. `CreateObject("Wscript.Shell").Run payload, 0`: This line creates a `Wscript.Shell` object and then uses its `Run` method to execute the program specified in the `payload` variable. The `0` parameter indicates that the program will run hidden, without displaying any window.

5. `End Sub`: This line marks the end of the subroutine.

Overall, when this macro is executed, it will launch the Windows Calculator program in the background, without showing any window or interface to the user. This type of macro could be used in automation tasks but should be handled with care, as it could potentially be used to execute malicious code.

You can have value zero to four, what they denote is the window style of the program being executed.

`0`: hide the window and activate another window.

`1`: activate and display the window. And if the window is minimized or maximized, the system restores it to its original size and position.

`2`: activate the window and display it as a minimized window.

`3`: activate the window and display it as a maximized window.

在您提到的代码 `CreateObject("Wscript.Shell").Run payload, 0` 中，数字 `0` 是 `Run` 方法的一个参数，用于控制程序运行时窗口的显示方式。这个参数被称为窗口样式，具体的值和它们的含义如下：

- **0** — 隐藏窗口并激活另一个窗口。程序在后台运行，用户无法看到任何窗口。
- **1** — 激活并显示窗口。如果窗口被最小化或最大化，Windows会恢复其大小。这是最常用的选项，使窗口正常显示。
- **2** — 激活窗口并将其以最小化的形式显示。
- **3** — 激活窗口并将其以最大化的形式显示。
- **4** — 显示窗口，但不激活它。窗口将以它最近的大小和位置显示，保持用户当前的活动窗口为激活状态。
- **5** — 激活窗口并在当前位置和大小下显示它。
- **6** — 最小化指定的窗口并激活下一个顶级窗口。
- **7** — 将窗口显示为最小化的形式，但不激活它。
- **8** — 以当前的状态显示窗口。
- **9** — 激活并显示窗口。如果窗口被最小化或最大化，Windows不会恢复其大小，而是保持原样。

通过选择不同的数字，开发者可以根据需要控制应用程序窗口的行为。在自动化脚本中，经常会选择 `0` 来确保程序在不干扰用户的情况下运行。

In the code `CreateObject("Wscript.Shell").Run payload, 0`, the number `0` is a parameter for the `Run` method that controls how the program's window is displayed when it runs. This parameter is known as the window style, and different values affect the visibility and behavior of the window. Here's a breakdown of these values and their meanings:

- **0** — Hides the window and activates another window. The program runs in the background, invisible to the user.
- **1** — Activates and displays the window. If the window is minimized or maximized, Windows restores it to its original size. This is the most commonly used option to show the window normally.
- **2** — Activates the window and displays it minimized.
- **3** — Activates the window and displays it maximized.
- **4** — Displays the window in its most recent size and position without activating it. The current active window remains active.
- **5** — Activates the window and displays it in its current size and position.
- **6** — Minimizes the specified window and activates the next top-level window.
- **7** — Displays the window as minimized but does not activate it.
- **8** — Displays the window in its current state.
- **9** — Activates and displays the window. If the window is minimized or maximized, Windows does not restore it but keeps it as is.

These different numbers allow developers to control the behavior of application windows as required. In automation scripts, `0` is often chosen to ensure that the program runs without disturbing the user.

False denote whether we want to wait for completion. It specifies whether the script should wait for the program to complete before continuing execution.

False: The script should not wait for the program to complete.

```VB
Sub Poc()
    Dim wsh As Object
    Set wsh = CreateObject("WScript.Shell")
    wsh.Run "notepad.exe", 1, False
End Sub
```

这段代码是用 Visual Basic for Applications (VBA) 编写的，定义了一个名为 `Poc` 的宏，其功能是在 Microsoft Office 应用程序（如 Excel 或 Word）中启动记事本（notepad.exe）。这是一个常见的方式来从 VBA 代码中运行外部程序。下面是代码的逐行解释：

1. **`Sub Poc()`**：这行定义了一个名为 `Poc` 的子程序。

2. **`Dim wsh As Object`**：这行声明了一个名为 `wsh` 的变量，类型为 `Object`。这个变量将用来存储对一个外部对象的引用，在本例中是 Windows 脚本宿主的一个实例。

3. **`Set wsh = CreateObject("WScript.Shell")`**：这行代码使用 `CreateObject` 函数创建一个 `WScript.Shell` 对象的实例，并将其赋值给 `wsh` 变量。`WScript.Shell` 是一个可以用来运行外部程序、访问环境变量等功能的对象。

4. **`wsh.Run "notepad.exe", 1, False`**：
   - **`"notepad.exe"`**：指定要运行的程序，这里是 Windows 的记事本应用。
   - **`1`**：指定窗口模式，这里的 `1` 表示激活并显示窗口，如果窗口最小化或最大化，Windows 将其恢复到正常大小。
   - **`False`**：指定是否等待程序执行完毕才继续执行 VBA 脚本。`False` 表示不等待，即 VBA 脚本将继续执行，而不管记事本程序是否仍在运行。

5. **`End Sub`**：标记子程序的结束。

这个宏在被触发时，会打开一个记事本窗口，允许用户查看或编辑文本。这种技术可以用来快速从 Office 应用程序中访问或启动其他 Windows 程序。

This code is written in Visual Basic for Applications (VBA) and defines a macro named `Poc` that is designed to launch Notepad (`notepad.exe`) from within a Microsoft Office application, such as Excel or Word. This is a common way to execute external programs from VBA code. Here's a line-by-line explanation of the code:

1. **`Sub Poc()`**: This line defines a subroutine named `Poc`.

2. **`Dim wsh As Object`**: This line declares a variable named `wsh` of type `Object`. This variable will be used to store a reference to an external object, in this case, an instance of the Windows Script Host.

3. **`Set wsh = CreateObject("WScript.Shell")`**: This line uses the `CreateObject` function to create an instance of the `WScript.Shell` object and assigns it to the `wsh` variable. `WScript.Shell` is an object that provides functionality for running external programs, accessing environment variables, and more.

4. **`wsh.Run "notepad.exe", 1, False`**:
   - **`"notepad.exe"`**: Specifies the program to run, which in this case is Windows' Notepad application.
   - **`1`**: Specifies the window mode, where `1` means to activate and display the window. If the window is minimized or maximized, Windows restores it to its normal size.
   - **`False`**: Specifies whether to wait for the program to finish executing before continuing with the VBA script. `False` means not to wait, allowing the VBA script to continue execution regardless of whether Notepad is still running.

5. **`End Sub`**: Marks the end of the subroutine.

When triggered, this macro opens a Notepad window, allowing the user to view or edit text. This technique can be used to quickly access or launch other Windows programs from within Office applications.

Once we're done creating our VBA macro, what do we do?

We close up the IDE, and then we go into `File` -> `Save As`:

`Save as type`: Word Macron-Enabled Document, that does work.

Word Document: standard Word document, which is docx. Your macros are not going to work.

![saveasdocm.png](/img/in-post/ine/saveasdocm.png)