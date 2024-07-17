---
layout: post
title: "VBA Macro Fundamentals"
date: "2024-07-16"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# VBA Macro Fundamentals VBA 宏基础
# Introduction to VBA VBA 简介
## Visual Basic Application (VBA) Visual Basic 应用程序 (VBA)
Visual Basic for Applications (VBA) is a programming language developed by Microsoft for automating tasks and extending the functionality of its Office suite of applications, including Excel, Word, PowerPoint, Access, and Outlook.

VBA scripting can be used to automate processes, interact with the Windows API, and implement user-defined functions. It also enables you to manipulate the user interface features of the host applications.

Microsoft Word and Excel allow users to embed VBA macros in documents/spreadsheets for the automation of manual tasks and management of dynamic data and for the linking of documents.

Visual Basic for Applications（VBA）是微软开发的一种编程语言，用于自动化任务并扩展其 Office 应用程序套件的功能，包括 Excel、Word、PowerPoint、Access 和 Outlook。

VBA 脚本可以用来自动化流程、与 Windows API 交互，并实现用户定义的函数。它还允许你操控宿主应用程序的用户界面功能。

Microsoft Word 和 Excel 允许用户在文档/电子表格中嵌入 VBA 宏，以自动化手动任务、管理动态数据以及链接文档。

## Visual Basic Application (VBA) Features Visual Basic 应用程序（VBA）特性
Purpose: VBA allows users to write programs (macros) to automate repetitive tasks, perform calculations, manipulate data, create custom forms, and interact with other applications.

Integration: VBA is tightly integrated into Microsoft Office applications, providing access to a rich set of objects, properties, methods, and events that allow for extensive customization and automation.

Syntax: VBA syntax is similar to other programming languages like Visual Basic (VB) and BASIC. It uses a combination of keywords, operators, variables, and control structures to create executable code.

目的：VBA 允许用户编写程序（宏），用于自动化重复性任务、执行计算、操作数据、创建自定义表单和与其他应用程序交互。

集成性：VBA 与 Microsoft Office 应用程序紧密集成，提供了丰富的对象、属性、方法和事件集，允许进行广泛的自定义和自动化。

语法：VBA 语法与 Visual Basic（VB）和 BASIC 等其他编程语言类似。它使用关键词、运算符、变量和控制结构的组合来创建可执行代码。

Development Environment: Each Office application includes a built-in VBA Integrated Development Environment (IDE) where users can write, edit, debug, and run VBA code. The IDE provides tools for code editing, debugging, and project management.

Objects and Methods: VBA allows users to work with objects that represent elements of the Office application (e.g., worksheets, cells, shapes) and manipulate them using methods and properties. For example, in Excel, you can write VBA code to automate data manipulation, chart creation, or report generation.

开发环境：每个 Office 应用程序都包括一个内置的 VBA 集成开发环境（IDE），用户可以在其中编写、编辑、调试和运行 VBA 代码。IDE 提供了代码编辑、调试和项目管理的工具。

对象和方法：VBA 允许用户使用代表 Office 应用程序元素（例如，工作表、单元格、形状）的对象，并通过方法和属性对它们进行操作。例如，在 Excel 中，你可以编写 VBA 代码来自动化数据操作、图表创建或报告生成。

# VBA Macros VBA 宏
## VBA Macros VBA 宏
In the context of client-side attacks and VBA (Visual Basic for Applications), macros refer to small programs or scripts written in VBA that automate tasks and extend the functionality of applications, particularly within the Microsoft Office suite.

Simply put, a Macro is a piece of VBA code embedded in Office documents.

Macros have been popularly weaponized to facilitate the execution of arbitrary code or executables.

在客户端攻击和 VBA（Visual Basic for Applications）的背景下，宏指的是用 VBA 编写的小程序或脚本，用于自动化任务并扩展应用程序的功能，特别是在 Microsoft Office 套件中。

简单地说，宏是嵌入在 Office 文档中的一段 VBA 代码。

宏已经广泛被用作武器化，以便于执行任意代码或可执行文件。

This Microsoft feature got abused by computer viruses in the late 1990s, mainly due its powerful functionality and the fact that, at the time, Office file macros were automatically executed by default.

As of MS Office 2003, this behavior was altered. Macros were no longer executed automatically when an Office file was loaded and a GUI would pop-up informing users of macro presence inside the file.

MS Office 2007 took macro security a step further. Macros could not be embedded at all within the default MS Word document file. This effort was facilitated by the OfficeOpen XML standard, based on which Microsoft introduced four distinct file formats.

这个微软的功能在 1990 年代末被计算机病毒滥用，主要是由于其强大的功能，以及当时 Office 文件中的宏默认会自动执行这一事实。

从 MS Office 2003 开始，这种行为发生了改变。当 Office 文件被加载时，宏不再自动执行，而且会弹出一个图形用户界面，通知用户文件内部存在宏。

MS Office 2007 将宏的安全性提升了一个层级。在默认的 MS Word 文档文件中根本无法嵌入宏。这一努力得益于 OfficeOpen XML 标准的支持，基于此标准，微软引入了四种不同的文件格式。

## MS Office File Formats MS Office 文件格式
| File Extension |       File Type        | Macros Permitted |
|:--------------:|:----------------------:|:----------------:|
|     DOCX       | compressed document    |        No        |
|     DOTX       | compressed template    |        No        |
|     DOCM       | compressed document    |       Yes        |
|     DOTM       | compressed template    |       Yes        |


| 文件扩展名 |       文件类型       | 允许宏 |
|:----------:|:--------------------:|:------:|
|    DOCX    | 压缩文档             |  否    |
|    DOTX    | 压缩模板             |  否    |
|    DOCM    | 压缩文档             |  是    |
|    DOTM    | 压缩模板             |  是    |

Microsoft Windows uses file extension to determine the software that will handle the opening of a file once it is clicked.

By the time Microsoft Office is installed, the above extensions are associated with it. Subsequently, all of the file types in the will be handled by the Microsoft Office suite of programs.

Microsoft Windows 使用文件扩展名来确定一旦点击文件后将由哪个软件来处理文件的打开。

在安装 Microsoft Office 之后，上述的扩展名将与其关联。随后，所有这些文件类型都将由 Microsoft Office 程序套件来处理。

Microsoft Word performs file data validation prior to opening a file. Data validation is performed in the form of data structure identification, against the OfficeOpen XML standard.

The validation is actually performed by MS Office’s WWLIB.DLL component.

The file name extension plays no role on this data validation process. If any error occurs during the data structure identification, the file being analyzed will not be opened.

在打开文件之前，Microsoft Word 会执行文件数据验证。数据验证的形式是数据结构识别，基于 OfficeOpen XML 标准进行。

实际上，验证是由 MS Office 的 WWLIB.DLL 组件执行的。

文件名扩展名在这个数据验证过程中没有任何作用。如果在数据结构识别过程中发生任何错误，正在分析的文件将不会被打开。

It should be noted that DOCM files containing macros can be renamed as other file formats by changing the file extension and still keep their macro executing capabilities.

For example, an RTF file does not support macros, by design, but a DOCM file renamed to RTF will be handled by Microsoft Word and will be capable of macro execution.

应当注意，包含宏的 DOCM 文件可以通过更改文件扩展名被重命名为其他文件格式，同时仍保持其宏执行功能。

例如，RTF 文件在设计上不支持宏，但是一个重命名为 RTF 的 DOCM 文件将由 Microsoft Word 处理，并且能够执行宏。

# WScript & VBA WScript 和 VBA
## WScript
WScript is a Windows Script Host object model that provides a scripting environment for executing scripts on Windows-based operating systems.

It allows users to run scripts written in scripting languages such as VBScript (Visual Basic Script) and JScript (JavaScript) directly from the command line or as part of automated processes.

In the context of VBA (Visual Basic for Applications), WScript can be utilized to extend the capabilities of VBA macros by enabling them to interact with the Windows operating system, execute external commands, manipulate files and folders, and perform other systemrelated tasks.

WScript 是一个 Windows 脚本宿主对象模型，为在基于 Windows 的操作系统上执行脚本提供了脚本环境。

它允许用户运行用诸如 VBScript（Visual Basic 脚本）和 JScript（JavaScript）这样的脚本语言编写的脚本，可以直接从命令行运行或作为自动化流程的一部分。

在 VBA（Visual Basic for Applications）的上下文中，WScript 可以用来扩展 VBA 宏的功能，使其能够与 Windows 操作系统交互、执行外部命令、操作文件和文件夹以及执行其他系统相关任务。

## WScript & VBA WScript 和 VBA
Accessing WScript Object Model: In VBA macros, you can create an instance of the WScript object to access its properties and methods. This allows you to perform various system-level operations, such as displaying messages, accessing environment variables, and running external programs.

Displaying Messages: Using WScript, you can display dialog boxes and messages to interact with users during the execution of VBA macros. For example, you can show informative messages, warnings, prompts for user input, or error notifications.

Running External Programs: WScript enables VBA macros to execute external programs or commands directly from within Office applications. This capability is useful for automating tasks that require interaction with other applications or executing system utilities.

访问 WScript 对象模型：在 VBA 宏中，你可以创建一个 WScript 对象的实例来访问其属性和方法。这使你能够执行各种系统级操作，例如显示消息、访问环境变量和运行外部程序。

显示消息：使用 WScript，你可以在执行 VBA 宏期间显示对话框和消息以与用户交互。例如，你可以显示信息性消息、警告、提示用户输入或错误通知。

运行外部程序：WScript 使 VBA 宏能够直接从 Office 应用程序内执行外部程序或命令。这种能力对于自动化需要与其他应用程序交互或执行系统工具的任务非常有用。

# VBA Macros & Client-Side Attacks VBA 宏与客户端攻击
## Macros in Client-Side Attacks 客户端攻击中的宏
Delivery Mechanism: Malicious actors often use macros embedded within documents (e.g., Word documents, Excel spreadsheets) as a delivery mechanism for malware. These documents are typically distributed via email or other channels, enticing users to enable macros, thereby executing the embedded malicious code.

Social Engineering: Macros are frequently employed in social engineering attacks, where users are tricked into enabling macros under the guise of accessing content or functionality within the document. Once enabled, the malicious macros execute, compromising the user's system.

传递机制：恶意行为者常常使用嵌入在文档中的宏（例如，Word 文档、Excel 电子表格）作为恶意软件的传递机制。这些文档通常通过电子邮件或其他渠道分发，诱使用户启用宏，从而执行嵌入的恶意代码。

社会工程学：宏在社会工程学攻击中经常被使用，其中用户在访问文档中的内容或功能的幌子下被诱骗启用宏。一旦启用，恶意宏就会执行，危害用户的系统。

Exploiting Vulnerabilities: Macros can exploit vulnerabilities in client-side applications, such as Microsoft Office, by leveraging features like macro scripting, ActiveX controls, and embedded objects. Vulnerabilities in these features can be exploited to execute arbitrary code on the victim's system.

Payload Delivery: Malicious macros often serve as a payload delivery mechanism, facilitating the execution of malware or other malicious actions on the victim's system. Once macros are enabled, they can download and execute additional payloads from remote servers, leading to further compromise.

利用漏洞：宏可以通过利用宏脚本、ActiveX 控件和嵌入对象等功能，来利用客户端应用程序（如 Microsoft Office）中的漏洞。这些功能中的漏洞可以被用来在受害者系统上执行任意代码。

载荷传递：恶意宏通常作为载荷传递机制，促进在受害者系统上执行恶意软件或其他恶意行为。一旦宏被启用，它们可以从远程服务器下载并执行额外的载荷，导致进一步的妥协。



