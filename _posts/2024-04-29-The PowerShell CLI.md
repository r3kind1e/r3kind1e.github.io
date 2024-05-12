---
layout: post
title: "The PowerShell CLI"
date: "2024-04-29"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - PowerShell for Pentesters
---

# The PowerShell CLI
## 2.1 The PowerShell CLI
+ For our first task, we should become familiar with the [PowerShell Command Line Interface](http://radar.oreilly.com/2013/06/powershell-command-line-introduction.html) (CLI).

+ The PowerShell CLI provides us with access to built-in cmdlets, modules, functions, features, and provides a way to create tasks, functions, variables interactively and more, directly from the CLI.

+ In most cases, accessing the CLI is as simple as just typing “powershell” in the Windows search field from the Start Menu.

+ Alternatively, the shortcut to PowerShell can be found within the “%appdata%\Microsoft\Windows\Start Menu\Programs\Windows PowerShell” directory.

+ In this case, the shortcuts are unavailable; the PowerShell executable itself can found in the “C:\Windows\System32\WindowsPowerShell\v1.0” directory.

+ If other versions are available on the system, they can be found in their corresponding version paths.

**A note regarding 32-bit and 64-bit PowerShell executables:**

+ If you’re operating on a 64-bit system, the location of the 64-bit PowerShell executable can be found in C:\windows\system32\WindowsPowerShell.

+ While the 32-bit version being located in the C:\windows\SysWOW64\WindowsPowerShell directory.

+ This can be a bit confusing considering the directory naming convention.

**A note regarding 32-bit and 64-bit PowerShell executables:**

+ Nonetheless, we can determine whether we’re running in a 32-bit or 64-bit PowerShell environment from the CLI with the following command:

```powershell
PS C:\> [Environment]::Is64BitProcess
```

+ Which should return “True” if the current PowerShell process is 64-bit:

```powershell
PS C:\> [Environment]::Is64BitProcess
True
```

**A note regarding 32-bit and 64-bit PowerShell executables:**

+ On a 32-bit system, the executable will be in its usual location of

```
C:\Windows\System32\WindowsPowerShell\*
```

# PowerShell 命令行界面
## 2.1 PowerShell 命令行界面
+ 对于我们的第一个任务，我们应该熟悉 [PowerShell 命令行界面](http://radar.oreilly.com/2013/06/powershell-command-line-introduction.html)（CLI）。

+ PowerShell CLI 为我们提供了访问内置 cmdlets、模块、功能、特性的途径，并提供了一种方式，可以直接从 CLI 交互式地创建任务、功能、变量等。

+ 在大多数情况下，访问 CLI 只需在开始菜单的 Windows 搜索字段中输入“powershell”。

+ 另外，PowerShell 的快捷方式可以在“%appdata%\Microsoft\Windows\Start Menu\Programs\Windows PowerShell”目录中找到。

+ 如果在这种情况下找不到快捷方式；PowerShell 可执行文件本身可以在“C:\Windows\System32\WindowsPowerShell\v1.0”目录中找到。

+ 如果系统上有其他版本可用，它们可以在相应的版本路径中找到。

**关于 32 位和 64 位 PowerShell 可执行文件的说明：**

+ 如果您在 64 位系统上操作，64 位 PowerShell 可执行文件的位置可以在 C:\windows\system32\WindowsPowerShell 找到。

+ 而 32 位版本位于 C:\windows\SysWOW64\WindowsPowerShell 目录。

+ 考虑到目录命名约定，这可能会有些混淆。

**关于 32 位和 64 位 PowerShell 可执行文件的说明：**

+ 尽管如此，我们可以使用以下命令在 CLI 中确定我们是在 32 位还是 64 位 PowerShell 环境中运行：

```powershell
PS C:\> [Environment]::Is64BitProcess
```

+ 如果当前的 PowerShell 进程是 64 位的，应该返回“True”：

```powershell
PS C:\> [Environment]::Is64BitProcess
True
```

**关于 32 位和 64 位 PowerShell 可执行文件的说明：**

+ 在 32 位系统上，可执行文件将位于其通常位置

```
C:\Windows\System32\WindowsPowerShell\*
```

+ When possible, we should try and launch PowerShell as the Administrator user as this will give us access to functions which we would be otherwise unable to access as a Lower-Privileged user.

+ We can right-click on the Shortcut or Executable and select “Run As Administrator.”

NOTE:
+ Although our examples are shown on Windows 10, all of the steps will be similar for Windows 7 and other versions of Windows, unless otherwise noted.

+ Once we launch PowerShell, we’re presented with the familiar blue console.

### 2.1.1 Basic Usage

+ Like most other programs found on Windows, the PowerShell executable has its own set of command line options. We can view these options with the usual “/?” help parameter:

+ 尽可能地，我们应该尝试以管理员身份启动 PowerShell，这将使我们能够访问一些作为低权限用户无法访问的功能。

+ 我们可以右键点击快捷方式或可执行文件并选择“以管理员身份运行”。

注意：
+ 虽然我们的示例显示在 Windows 10 上，但所有步骤对于 Windows 7 和其他版本的 Windows 都是相似的，除非另有说明。

+ 一旦我们启动 PowerShell，我们会看到熟悉的蓝色控制台。

### 2.1.1 基本使用

+ 像 Windows 上的大多数其他程序一样，PowerShell 可执行文件有自己的一套命令行选项。我们可以用常见的“/?”帮助参数来查看这些选项：

```
PS C:\windows\system32> powershell /?

•       PowerShell[.exe] [-PSConsoleFile <file> | -Version <version>]
    [-NoLogo] [-NoExit] [-Sta] [-Mta] [-NoProfile] [-NonInteractive]
    [-InputFormat {Text | XML}] [-OutputFormat {Text | XML}]
    [-WindowStyle <style>] [-EncodedCommand <Base64EncodedCommand>]
    [-ConfigurationName <string>]
    [-File <filePath> <args>] [-ExecutionPolicy <ExecutionPolicy>]
    [-Command { - | <script-block> [-args <arg-array>]
                  | <string> [<CommandParameters>] } ]

PowerShell[.exe] -Help | -? | /?

-PSConsoleFile
    加载指定的 Windows PowerShell 控制台文件。要创建控制台
    文件，请使用 Windows PowerShell 中的 Export-Console。

-Version
    启动指定版本的 Windows PowerShell。
    通过参数输入一个版本号，例如 "-version 2.0"。

-NoLogo
    在启动时隐藏版权横幅。

-NoExit
    运行启动命令后请勿退出。

-Sta
    使用单线程单元启动 shell。
    单线程单元(STA)是默认设置。

-Mta
    使用多线程单元启动 shell。

-NoProfile
    不要加载 Windows PowerShell 配置文件。

-NonInteractive
    不要向用户显示交互式提示。

-InputFormat
    描述发送到 Windows PowerShell 的数据的格式。有效值为
    "Text" (文本字符串)或 "XML" (序列化的 CLIXML 格式)。

-OutputFormat
    确定来自 Windows PowerShell 的输出是如何设置格式的。有效值
    为 "Text" (文本字符串)或 "XML" (序列化的 CLIXML 格式)。

-WindowStyle
    将窗口样式设置为“Normal”、“Minimized”、“Maximized”或“Hidden”。

-EncodedCommand
    接受命令以 base-64 编码的字符串版本。使用此参数
    将命令提交到需要复杂引号或大括号的 Windows PowerShell
    。

-ConfigurationName
    指定运行 Windows PowerShell 的配置终结点。
    这可以是本地计算机上注册的任何终结点，包括
    默认的 Windows PowerShell 远程终结点或具有特定用户角色功能的自定义终结点
    。

-File
    在本地范围("dot-sourced")内运行指定的脚本，以便
    脚本创建的函数和变量可在当前会话中使用
    。输入脚本文件路径和任何参数。
    File 必须是命令中的最后一个参数，因为在 File 参数名称之后输入的所有字符
    都解释
    为脚本文件路径后跟脚本参数。

-ExecutionPolicy
    为当前会话设置默认执行策略并将其保存在
    $env:PSExecutionPolicyPreference 环境变量中。
    此参数不会更改注册表中设置的 Windows PowerShell 执行策略
    。

-Command
    执行指定的命令(及任何参数)，如同它们是
    在 Windows PowerShell 命令提示符处键入的，然后再退出，除非指定了
    NoExit。Command 的值可以为 "-"、字符串或
    脚本块。

    如果 Command 的值为 "-"，则从标准输入中读取命令文本
    。

    如果 Command 的值为脚本块，则脚本块必须用括号({})括起来
    。只能在 Windows PowerShell 中运行 PowerShell.exe 时指定脚本块
    。脚本块的结果会返回到
    父 shell 中，如 XML 对象(而不是实时对象)中所述。

    如果 Command 的值为字符串，则 Command 必须是命令中的最后一个参数
    ，因为在命令之后键入的任何字符都
    被解释为命令属性。

    要写入运行 Windows PowerShell 命令的字符串，请使用以下格式:
        "& {<command>}"
    其中，引号表示是一个字符串，而调用操作符(&)
    会导致命令被执行。

-Help, -?, /?
    显示此消息。如果在 Windows PowerShell 中键入 PowerShell.exe 命令
    ，则使用连字符(-)而不是正斜杠(/)来追加命令参数
    。可在 Cmd.exe 中使用连字符或正斜杠。

EXAMPLES
    PowerShell -PSConsoleFile SqlSnapIn.Psc1
    PowerShell -version 2.0 -NoLogo -InputFormat text -OutputFormat XML
    PowerShell -ConfigurationName AdminRoles
    PowerShell -Command {Get-EventLog -LogName security}
    PowerShell -Command "& {Get-EventLog -LogName security}"

    # To use the -EncodedCommand parameter:
    $command = 'dir "c:\program files" '
    $bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
    $encodedCommand = [Convert]::ToBase64String($bytes)
    powershell.exe -encodedCommand $encodedCommand
```

+ These options can alternatively be shown with the “-Help” parameter or “-?” as well and will be most useful when we’re calling PowerShell from a standard Windows command prompt. (cmd.exe)

+ 这些选项也可以通过“-Help”参数或“-?”来显示，当我们从标准 Windows 命令提示符（cmd.exe）调用 PowerShell 时，这将非常有用。

The following are some of the more common PowerShell.exe command line parameters we will use for our purposes:

**-ExecutionPolicy**

+ The PowerShell execution policy determines which scripts if any, we can run and can easily be disabled with the “Bypass” or “Unrestricted” arguments.

```powershell
C:\> powershell.exe -ExecutionPolicy Bypass .\script.ps1
C:\> powershell.exe -ExecutionPolicy Unrestricted .\script.ps1
```

PowerShell 的 `-ExecutionPolicy` 参数是用来控制 PowerShell 脚本的执行策略的。这个参数可以定义哪些类型的脚本（如本地脚本、下载的脚本等）可以在系统上运行。这是一种安全功能，旨在防止未经授权的或恶意的脚本执行。

以下是一些常用的执行策略：

1. **Restricted**（限制）：这是默认的执行策略。在此策略下，不允许运行任何脚本。用户只能输入单个命令。

2. **AllSigned**（全部签名）：在此策略下，只能运行由可信发布者签名的脚本。首次运行某个签名的脚本时，系统会提示你是否信任签名者。

3. **RemoteSigned**（远程签名）：在此策略下，所有从互联网下载的脚本必须由可信发布者签名才能运行。本地未签名的脚本可以运行。

4. **Unrestricted**（无限制）：此策略允许运行所有脚本。如果运行的脚本是从互联网下载的，PowerShell 会警告你该脚本已从互联网下载。

5. **Bypass**（绕过）：在此策略下，所有的脚本都可以运行，PowerShell 不会对任何脚本执行任何限制。

6. **Undefined**（未定义）：此策略意味着没有设置执行策略。如果所有范围的执行策略都设置为 Undefined，将使用默认的 Restricted 策略。

你可以使用 `Set-ExecutionPolicy` 命令来设置执行策略，例如：

```powershell
Set-ExecutionPolicy RemoteSigned
```

这将设置执行策略为 RemoteSigned。要查看当前的执行策略，可以使用：

```powershell
Get-ExecutionPolicy
```

这些设置有助于管理不同的安全级别，以适应不同的安全需求。

The `-ExecutionPolicy` parameter in PowerShell is used to control the execution policies of PowerShell scripts. This parameter defines which types of scripts, such as local scripts or downloaded scripts, can be run on the system. It serves as a security feature intended to prevent the execution of unauthorized or malicious scripts.

Here are some commonly used execution policies:

1. **Restricted**: This is the default execution policy. Under this policy, no scripts are allowed to run. Users can only input individual commands.

2. **AllSigned**: With this policy, only scripts that have been signed by a trusted publisher can be run. The first time you run a signed script, the system will prompt you to confirm whether you trust the signer.

3. **RemoteSigned**: This policy allows scripts downloaded from the internet to run only if they are signed by a trusted publisher. Local scripts that are not signed can also run.

4. **Unrestricted**: This policy allows all scripts to run. PowerShell will warn you if you're running a script that was downloaded from the internet.

5. **Bypass**: Under this policy, all scripts can run without any restrictions imposed by PowerShell.

6. **Undefined**: This policy means no execution policy is set. If the execution policy is set to Undefined at all scopes, the default Restricted policy is used.

You can use the `Set-ExecutionPolicy` command to set the execution policy, for example:

```powershell
Set-ExecutionPolicy RemoteSigned
```

This sets the execution policy to RemoteSigned. To check the current execution policy, you can use:

```powershell
Get-ExecutionPolicy
```

These settings help manage different levels of security to accommodate varying security needs.

**-WindowStyle:**

+ The -WindowStyle parameter hides the Powershell window when used with the “hidden” argument.

```powershell
C:\> powershell.exe -WindowStyle Hidden .\script.ps1
```

在 PowerShell 中，`-WindowStyle` 参数用于控制 PowerShell 窗口的显示样式。这个参数可以在启动 PowerShell 脚本或命令行时设置，以确定窗口的外观。

以下是 `-WindowStyle` 参数可以接受的几种值：

1. **Normal**：设置 PowerShell 窗口为正常大小。这是大多数情况下的默认值。

2. **Minimized**：启动时将 PowerShell 窗口最小化。窗口会显示在任务栏上，但不会在屏幕上显示其内容。

3. **Maximized**：将 PowerShell 窗口最大化以填充整个屏幕。

4. **Hidden**：启动 PowerShell 窗口时不显示窗口。窗口在后台运行，用户界面不可见，适用于需要静默运行脚本的情况。

例如，如果你想要启动一个 PowerShell 脚本，并且希望窗口最小化，你可以在命令中使用 `-WindowStyle Minimized` 参数，像这样：

```powershell
powershell.exe -WindowStyle Minimized -File "C:\Path\To\Script.ps1"
```

这将启动名为 "Script.ps1" 的脚本，并且 PowerShell 窗口将以最小化的形式出现。使用这个参数可以根据脚本的运行需求和用户的偏好调整窗口的显示状态。

In PowerShell, the `-WindowStyle` parameter is used to control the display style of the PowerShell window. This parameter can be set when launching PowerShell scripts or command lines to determine the appearance of the window.

Here are the various values that the `-WindowStyle` parameter can accept:

1. **Normal**: Sets the PowerShell window to a normal size. This is the default value in most cases.

2. **Minimized**: Minimizes the PowerShell window at launch. The window will appear in the taskbar but will not display its content on the screen.

3. **Maximized**: Maximizes the PowerShell window to fill the entire screen.

4. **Hidden**: Launches the PowerShell window without displaying it. The window runs in the background with its user interface invisible, suitable for situations where scripts need to run silently.

For example, if you want to launch a PowerShell script and prefer the window to be minimized, you can use the `-WindowStyle Minimized` parameter in your command like this:

```powershell
powershell.exe -WindowStyle Minimized -File "C:\Path\To\Script.ps1"
```

This command will start the script named "Script.ps1" with the PowerShell window appearing minimized. Using this parameter allows you to adjust the window's display state based on the script's operational needs and user preferences.

在 PowerShell 中，`-Command` 参数用于从命令行直接执行指定的 PowerShell 命令或脚本块。这个参数非常灵活，可以用来运行单个命令、一系列命令，或者整个脚本内容。

以下是使用 `-Command` 参数的几个关键点：

1. **单个命令**：你可以直接在 `-Command` 后面跟上要执行的命令。例如：
   ```powershell
   powershell -Command "Get-Process"
   ```
   这条命令会列出当前系统中的所有进程。

2. **命令序列**：你可以通过分号将多个命令分开，依次执行。例如：
   ```powershell
   powershell -Command "Get-Date; Get-Process"
   ```
   这条命令会先显示当前日期和时间，然后列出所有进程。

3. **脚本块**：使用 `-Command` 还可以执行一个完整的脚本块。脚本块需要放在花括号中。例如：
   ```powershell
   powershell -Command "{Get-Process | Where-Object {$_.CPU -gt 100}}"
   ```
   这会执行一个脚本块，列出所有 CPU 使用超过 100 的进程。

4. **从文件执行**：虽然通常使用 `-File` 参数来运行脚本文件，`-Command` 也可以用来执行文件中的脚本。例如：
   ```powershell
   powershell -Command ". 'C:\Path\To\Script.ps1'"
   ```
   注意点号和脚本路径之间的空格，它表示在当前会话中点源（运行）该脚本。

`-Command` 参数提供了在不同情境下快速执行 PowerShell 命令的能力，无论是直接在命令行中，还是通过脚本文件。它是自动化任务和管理系统时常用的工具。


**-Command:**

+ The -Command parameter is used to specify a Command or [Script Block](https://learn.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/about/about_script_blocks?view=powershell-7.4&viewFallbackFrom=powershell-6) to run.

```powershell
C:\> powershell.exe -Command Get-Process
C:\> powershell.exe -Command “& { Get-EventLog –LogName security }”
```

在 PowerShell 中，`-Command` 参数用于从命令行直接执行指定的 PowerShell 命令或脚本块。这个参数非常灵活，可以用来运行单个命令、一系列命令，或者整个脚本内容。

以下是使用 `-Command` 参数的几个关键点：

1. **单个命令**：你可以直接在 `-Command` 后面跟上要执行的命令。例如：
   ```powershell
   powershell -Command "Get-Process"
   ```
   这条命令会列出当前系统中的所有进程。

2. **命令序列**：你可以通过分号将多个命令分开，依次执行。例如：
   ```powershell
   powershell -Command "Get-Date; Get-Process"
   ```
   这条命令会先显示当前日期和时间，然后列出所有进程。

3. **脚本块**：使用 `-Command` 还可以执行一个完整的脚本块。脚本块需要放在花括号中。例如：
   ```powershell
   powershell -Command "{Get-Process | Where-Object {$_.CPU -gt 100}}"
   ```
   这会执行一个脚本块，列出所有 CPU 使用超过 100 的进程。

4. **从文件执行**：虽然通常使用 `-File` 参数来运行脚本文件，`-Command` 也可以用来执行文件中的脚本。例如：
   ```powershell
   powershell -Command ". 'C:\Path\To\Script.ps1'"
   ```
   注意点号和脚本路径之间的空格，它表示在当前会话中点源（运行）该脚本。

`-Command` 参数提供了在不同情境下快速执行 PowerShell 命令的能力，无论是直接在命令行中，还是通过脚本文件。它是自动化任务和管理系统时常用的工具。

In PowerShell, the `-Command` parameter is used to execute specified PowerShell commands or script blocks directly from the command line. This parameter is highly versatile and can be used to run a single command, a series of commands, or an entire script block.

Here are some key points about using the `-Command` parameter:

1. **Single Command**: You can execute a command directly following the `-Command`. For example:
   ```powershell
   powershell -Command "Get-Process"
   ```
   This command will list all the processes currently running on the system.

2. **Command Sequence**: You can separate multiple commands with semicolons to execute them sequentially. For example:
   ```powershell
   powershell -Command "Get-Date; Get-Process"
   ```
   This command will first display the current date and time, followed by listing all processes.

3. **Script Block**: The `-Command` can also be used to execute a complete script block, which needs to be enclosed in curly braces. For example:
   ```powershell
   powershell -Command "{Get-Process | Where-Object {$_.CPU -gt 100}}"
   ```
   This executes a script block that lists all processes where the CPU usage is greater than 100.

4. **Executing from a File**: Although the `-File` parameter is typically used to run script files, `-Command` can also execute scripts contained in files. For example:
   ```powershell
   powershell -Command ". 'C:\Path\To\Script.ps1'"
   ```
   Note the space between the dot and the script path, which indicates that the script is being sourced (executed) in the current session.

The `-Command` parameter provides the ability to quickly execute PowerShell commands in various contexts, whether directly on the command line or through script files. It is a commonly used tool for automating tasks and managing systems.

**-EncodedCommand:**

+ The -EncodedCommand parameter is used to execute base64 encoded scripts or commands.

```powershell
C:\> powershell.exe -EncodedCommand $encodedCommand
```

在 PowerShell 中，`-EncodedCommand` 参数用于执行经过 Base64 编码的 PowerShell 命令。这个参数的主要用途是允许在命令行中运行复杂的脚本或命令，同时避免常规命令行解析可能引起的问题，如特殊字符处理或长命令的断行问题。

使用 `-EncodedCommand` 的步骤通常包括：

1. **编写 PowerShell 脚本或命令**：首先，你需要准备你想要执行的 PowerShell 命令或脚本。

2. **进行 Base64 编码**：将 PowerShell 命令或脚本转换成 Base64 编码格式。这通常可以通过 PowerShell 本身来完成。例如，如果你有一个 PowerShell 命令 "Get-Process"，你可以使用以下命令来生成 Base64 编码字符串：
   ```powershell
   $command = 'Get-Process'
   $bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
   $encodedCommand = [Convert]::ToBase64String($bytes)
   ```

3. **使用 `-EncodedCommand` 参数执行**：一旦你有了 Base64 编码的命令，你就可以使用 `-EncodedCommand` 参数来执行它。例如：
   ```powershell
   powershell -EncodedCommand <Base64String>
   ```
   这里 `<Base64String>` 是你之前生成的 Base64 编码字符串。

这种方法特别适用于需要通过远程执行或在复杂环境中自动化脚本执行的场景。它也可以帮助绕过某些安全限制，因为编码的命令在外观上不像典型的 PowerShell 命令。然而，使用这种方法时也应注意安全风险，确保只执行已知安全的脚本和命令。

In PowerShell, the `-EncodedCommand` parameter is used to execute commands or scripts that have been encoded in Base64. The primary purpose of this parameter is to allow complex scripts or commands to be run on the command line while avoiding issues related to conventional command line parsing, such as handling special characters or line breaks in long commands.

The steps to use `-EncodedCommand` typically include:

1. **Writing the PowerShell Script or Command**: First, you need to prepare the PowerShell command or script you want to execute.

2. **Encoding to Base64**: Convert the PowerShell command or script into a Base64-encoded format. This can typically be done using PowerShell itself. For example, if you have a command like "Get-Process", you can use the following commands to generate a Base64-encoded string:
   ```powershell
   $command = 'Get-Process'
   $bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
   $encodedCommand = [Convert]::ToBase64String($bytes)
   ```

3. **Executing with `-EncodedCommand`**: Once you have the Base64-encoded command, you can execute it using the `-EncodedCommand` parameter. For example:
   ```powershell
   powershell -EncodedCommand <Base64String>
   ```
   Here, `<Base64String>` is the Base64-encoded string you generated earlier.

This method is particularly useful for scenarios that require scripts to be executed remotely or in complex environments for automation purposes. It can also help circumvent certain security restrictions since the encoded command does not visually resemble typical PowerShell commands. However, it is important to be cautious about security risks and ensure that only known safe scripts and commands are executed using this method.

**-NoProfile:**

+ Don’t load any powershell profiles.

+ Profiles are essentially scripts that run when the powershell executable is launched and can interfere with our operations.

```powershell
C:\> powershell.exe -NoProfile .\script.ps1
```

在 PowerShell 中，`-NoProfile` 参数用于启动 PowerShell 会话时不加载用户的配置文件（profile）。PowerShell 配置文件是一个特殊的脚本文件，它在 PowerShell 启动时自动运行。这些配置文件通常用于配置环境设置、加载模块、定义函数等。

使用 `-NoProfile` 参数的主要好处包括：

1. **提高启动速度**：因为不加载任何配置文件，所以 PowerShell 的启动时间可以显著减少，这在自动化脚本或需要频繁启动和关闭 PowerShell 的情况下尤其有用。

2. **避免配置冲突**：在一些特定的环境中，用户配置文件中可能包含修改环境变量或执行不需要的初始化脚本的命令。使用 `-NoProfile` 可以确保 PowerShell 会话保持一个干净的状态，不受这些个人或特定设置的影响。

3. **一致性**：在不同用户或系统之间运行脚本时，`-NoProfile` 参数可以确保脚本的行为不会因为不同的配置文件而有所不同，从而保持一致性。

例如，如果你想要确保在运行一个特定的脚本时不受任何用户配置的影响，可以使用以下命令：
```powershell
powershell.exe -NoProfile -File "C:\Path\To\Script.ps1"
```

这条命令将启动一个新的 PowerShell 会话，运行指定的脚本，但不加载任何用户配置文件。这样可以提高脚本执行的可预测性和一致性。

In PowerShell, the `-NoProfile` parameter is used to start a PowerShell session without loading the user's profile. A PowerShell profile is a special script file that runs automatically when PowerShell starts. These profiles are typically used to configure environment settings, load modules, define functions, and more.

The main benefits of using the `-NoProfile` parameter include:

1. **Faster Startup**: Because no profiles are loaded, the startup time for PowerShell can be significantly reduced. This is particularly useful in automation scripts or situations where PowerShell needs to be launched and closed frequently.

2. **Avoid Configuration Conflicts**: In certain environments, user profiles might contain commands that modify environment variables or execute unwanted initialization scripts. Using `-NoProfile` ensures that the PowerShell session remains clean and unaffected by these personal or specific settings.

3. **Consistency**: When running scripts across different users or systems, the `-NoProfile` parameter can ensure that the behavior of the script remains consistent, regardless of differing profile configurations.

For example, if you want to ensure that a specific script runs without any influence from user configurations, you could use the following command:
```powershell
powershell.exe -NoProfile -File "C:\Path\To\Script.ps1"
```

This command launches a new PowerShell session to run the specified script, but without loading any user profiles. This approach enhances the predictability and consistency of script execution.


**-Version:**

We can use the `-Version` parameter followed by a version number as the argument to downgrade the version of PowerShell.

+ Useful in scenarios where you’ve landed on a machine with a more recent version and need to downgrade to Version 1.0 or 2.0 or to complete certain tasks.

+ Requires that older versions are still installed on the target.

```powershell
C:\> powershell.exe –Version 2
```

在 PowerShell 中，`-Version` 参数用于启动 PowerShell 时指定一个特定的 PowerShell 版本。这个参数主要用于兼容性测试或在同一台机器上运行多个版本的 PowerShell 时确保脚本在指定的版本下运行。

使用 `-Version` 参数的主要注意事项如下：

1. **指定版本**：当你使用 `-Version` 参数时，你需要指定你想运行的 PowerShell 的版本号。例如，如果你想确保使用 PowerShell 5.0 版本来运行脚本，你可以使用以下命令：
   ```powershell
   powershell.exe -Version 5.0
   ```
   这将确保启动的 PowerShell 会话使用的是版本 5.0。

2. **向下兼容**：`-Version` 参数允许向下兼容较旧的版本，但不能启动高于机器上安装的最高版本的 PowerShell。如果尝试启动未安装的更高版本，将会出现错误。

3. **测试和开发**：这个参数在开发和测试环境中非常有用，特别是当你需要确保脚本在不同版本的 PowerShell 环境中表现一致时。

4. **限制**：在最新版本的 PowerShell，如 PowerShell 7 中，`-Version` 参数已被废弃，因为 PowerShell Core（版本 6 及以上）被设计为与 Windows PowerShell（版本 5.1 及以下）并行存在，而不是替代它。因此，在 PowerShell Core 中使用 `-Version` 参数将不起作用。

总的来说，`-Version` 参数对于确保脚本在预期的环境中以正确的方式执行提供了有力的控制，尤其是在多版本的 PowerShell 环境中工作时。

In PowerShell, the `-Version` parameter is used to specify a particular version of PowerShell when launching it. This parameter is primarily useful for compatibility testing or ensuring that scripts run under a specific version when multiple versions of PowerShell are installed on the same machine.

Here are some key points to consider when using the `-Version` parameter:

1. **Specifying the Version**: When you use the `-Version` parameter, you need to specify the version number of PowerShell you want to run. For example, if you want to ensure that a script runs using PowerShell version 5.0, you could use the following command:
   ```powershell
   powershell.exe -Version 5.0
   ```
   This ensures that the PowerShell session launched is using version 5.0.

2. **Backward Compatibility**: The `-Version` parameter allows for backward compatibility with older versions but cannot launch a version higher than the highest version installed on the machine. Attempting to launch an uninstalled higher version will result in an error.

3. **Testing and Development**: This parameter is very useful in development and testing environments, especially when you need to ensure that scripts perform consistently across different versions of PowerShell.

4. **Limitations**: In the latest versions of PowerShell, such as PowerShell 7, the `-Version` parameter has been deprecated. This is because PowerShell Core (version 6 and above) is designed to exist alongside Windows PowerShell (version 5.1 and below), not replace it. Therefore, using the `-Version` parameter in PowerShell Core will not have any effect.

Overall, the `-Version` parameter provides strong control over ensuring that scripts execute correctly in the intended environment, particularly useful when working in a multi-version PowerShell environment.

+ Furthermore, all of the PowerShell.exe command line parameters, as well as their arguments, can also be abbreviated, as long as the abbreviations are unique, and additionally, are not required to be case-sensitive either:

| -ExecutionPolicy Bypass | -EncodedCommand | -WindowStyle Hidden |
|----------|----------|----------|
| powershell.exe -ep Bypass | powershell.exe –enco | powershell.exe –W h |
| powershell.exe -ex by | powershell.exe -ec | powershell.exe –Wi hi |

In PowerShell, as in many command-line interfaces, you can use abbreviations for both command-line parameters and their arguments. This functionality is particularly useful for reducing the amount of typing required and speeding up work flow, but it can also make commands easier to read and write, especially for frequent users.

1. **Parameter Abbreviations**

PowerShell allows you to abbreviate the names of parameters as long as the abbreviation is unique enough to unambiguously identify the parameter. For example, if you have a parameter called `-ComputerName`, you can abbreviate it to `-Comp` if there are no other parameters starting with those letters in the particular cmdlet you are using.

- **Example**: Using `Get-Service` cmdlet
  ```powershell
  Get-Service -Name "WinRM"  # Full parameter name
  Get-Service -Na "WinRM"    # Abbreviated parameter name
  ```

2. **Argument Abbreviations**

PowerShell cmdlets often accept enumerated values (like `On`, `Off`, `All`) for parameters. PowerShell allows abbreviating these arguments as well. For instance, if a parameter accepts values such as `Minimal`, `Normal`, and `Detailed`, you might be able to use just `Min`, `Nor`, and `Det` respectively, provided these abbreviations are unique and clear in the context.

- **Example**: Using `Format-List` cmdlet with `View` parameter
  ```powershell
  Get-Process | Format-List -View Normal  # Full argument name
  Get-Process | Format-List -View Nor     # Abbreviated argument name
  ```

3. **Tab Completion**

PowerShell supports tab completion for commands, parameters, and sometimes arguments. This feature can automatically complete a command or parameter name when you press the tab key after typing part of the name. This helps in quickly writing commands without needing to remember the full names of parameters or their arguments.

4. **Using Aliases for Cmdlets**

PowerShell also includes aliases for many of its cmdlets, which are predefined short names. For example, `Get-Command` can be abbreviated to `gcm`, `Get-ChildItem` to `gci`, etc. These are not exactly parameter abbreviations but serve a similar purpose in reducing typing.

5. **Practical Considerations**

While abbreviating parameters and arguments can make commands shorter and faster to type, it's important to consider readability, especially in shared scripts. Using full parameter names in scripts can make them easier to understand for others or when revisiting your own scripts after some time.

Conclusion

Abbreviations in PowerShell for command-line parameters and arguments enhance efficiency and usability for users, especially those familiar with the commands and their context. However, balance is key, as excessive abbreviation can reduce the clarity and maintainability of scripts.
+ We will cover more of the PowerShell.exe Command Line options a bit later in modules that follow.

在 PowerShell 中，你可以缩写命令行参数和它们的参数，这在许多命令行接口中是常见的功能。这种功能特别适用于减少打字量和加快工作流程，同时也可以使命令更易于读写，特别是对于常用用户。

1. **参数缩写**

PowerShell 允许你缩写参数名称，只要缩写足够唯一，能够无歧义地识别参数。例如，如果有一个参数叫做 `-ComputerName`，你可以将其缩写为 `-Comp`，前提是在你使用的特定 cmdlet 中没有其他以这些字母开头的参数。

- **示例**：使用 `Get-Service` cmdlet
  ```powershell
  Get-Service -Name "WinRM"  # 完整参数名称
  Get-Service -Na "WinRM"    # 参数缩写
  ```

2. **参数值缩写**

PowerShell 的 cmdlet 经常接受枚举值（如 `On`、`Off`、`All`）作为参数。PowerShell 允许缩写这些参数值。例如，如果一个参数接受 `Minimal`、`Normal` 和 `Detailed` 这样的值，你可能可以使用 `Min`、`Nor` 和 `Det` 这样的缩写，只要这些缩写在上下文中唯一且清晰即可。

- **示例**：使用 `Format-List` cmdlet 的 `View` 参数
  ```powershell
  Get-Process | Format-List -View Normal  # 完整参数值名称
  Get-Process | Format-List -View Nor     # 参数值缩写
  ```

3. **Tab 键补全**

PowerShell 支持命令、参数，有时候还包括参数值的 Tab 键补全功能。当你在键入名称的部分内容后按 Tab 键时，这个功能可以自动完成命令或参数名称。这有助于快速输入命令，无需记住参数或其参数值的完整名称。

4. **使用 Cmdlet 的别名**

PowerShell 还为许多 cmdlet 包含预定义的短名，即别名。例如，`Get-Command` 可以缩写为 `gcm`，`Get-ChildItem` 可以缩写为 `gci` 等。这些不完全是参数缩写，但在减少打字方面发挥了类似的作用。

5. **实际考虑**

虽然缩写参数和参数值可以使命令更短、更快速地输入，但在共享脚本中考虑到可读性是很重要的。在脚本中使用完整的参数名可以使它们对其他人或在一段时间后重新查看自己的脚本时更易于理解。

结论

在 PowerShell 中对命令行参数和参数值进行缩写可以提高用户的效率和使用便利性，尤其是对于熟悉命令和上下文的用户。然而，关键在于平衡，因为过度缩写可能会降低脚本的清晰度和可维护性。

以下是我们将使用的一些更常见的 PowerShell.exe 命令行参数：

**-ExecutionPolicy**

+ PowerShell 执行策略决定我们可以运行哪些脚本，可以通过“Bypass”或“Unrestricted”参数轻松禁用。

```powershell
C:\> powershell.exe -ExecutionPolicy Bypass .\script.ps1
C:\> powershell.exe -ExecutionPolicy Unrestricted .\script.ps1
```

**-WindowStyle：**

+ -WindowStyle 参数在与“hidden”参数一起使用时，会隐藏 PowerShell 窗口。

```powershell
C:\> powershell.exe -WindowStyle Hidden .\script.ps1
```

**-Command：**

+ -Command 参数用于指定要运行的命令或[脚本块](https://learn.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/about/about_script_blocks?view=powershell-7.4&viewFallbackFrom=powershell-6)。

```powershell
C:\> powershell.exe -Command Get-Process
C:\> powershell.exe -Command “& { Get-EventLog –LogName security }”
```

**-EncodedCommand：**

+ -EncodedCommand 参数用于执行 base64 编码的脚本或命令。

```powershell
C:\> powershell.exe -EncodedCommand $encodedCommand
```

**-NoProfile：**

+ 不加载任何 PowerShell 配置文件。

+ 配置文件本质上是在启动 PowerShell 可执行文件时运行的脚本，可能会干扰我们的操作。

```powershell
C:\> powershell.exe -NoProfile .\script.ps1
```

**-Version：**

我们可以使用 `-Version` 参数，后跟版本号作为参数来降级 PowerShell 的版本。

+ 在您访问安装了较新版本且需要降级到版本 1.0 或 2.0 或完成某些任务的机器的情况下很有用。

+ 需要目标机器上仍然安装有较旧的版本。

```powershell
C:\> powershell.exe –Version 2
```

+ 此外，所有的 PowerShell.exe 命令行参数及其参数都可以缩写，只要缩写是唯一的，并且也不需要区分大小写：

| -ExecutionPolicy Bypass | -EncodedCommand | -WindowStyle Hidden |
|----------|----------|----------|
| powershell.exe -ep Bypass | powershell.exe –enco | powershell.exe –W h |
| powershell.exe -ex by | powershell.exe -ec | powershell.exe –Wi hi |

+ 我们将在稍后的模块中更多地介绍 PowerShell.exe 命令行选项。

#### 2.1.1.1 Get-Help
+ An extremely useful feature of the PowerShell CLI is the “[Get-Help](https://learn.microsoft.com/en-us/previous-versions/system-center/virtual-machine-manager-2008-r2/cc764318(v=technet.10)?redirectedfrom=MSDN)” cmdlet.

+ Similar to *nix “Man Pages,” we can call upon the “Get-Help” command to obtain information related to any function, alias, module or cmdlet that PowerShell is aware of.

+ We can do this by including the cmdlet, function or module name we’re looking for information on, as an argument to the “Get-Help” cmdlet.

+ PowerShell CLI 的一个极其有用的功能是 “[Get-Help](https://learn.microsoft.com/en-us/previous-versions/system-center/virtual-machine-manager-2008-r2/cc764318(v=technet.10)?redirectedfrom=MSDN)” cmdlet。

+ 类似于 *nix 的“手册页（Man Pages）”，我们可以调用 “Get-Help” 命令来获取有关 PowerShell 已知的任何函数、别名、模块或 cmdlet 的信息。

+ 我们可以通过将我们正在查找信息的 cmdlet、函数或模块名称作为参数包含在 “Get-Help” cmdlet 中来实现这一点。

+ We can see here that we’re requesting the PowerShell Help pages for the “Get-Help” cmdlet itself:

+ 我们可以看到，我们正在请求 “Get-Help” cmdlet 本身的 PowerShell 帮助页面：

```
PS C:\Windows\system32> Get-Help Get-Help

名称
    Get-Help

摘要
    Displays information about PowerShell commands and concepts.


语法
    Get-Help [[-Name] <System.String>] [-Category {Alias | Cmdlet | Provider | General | FAQ | Glossary | HelpFile | Sc
    riptCommand | Function | Filter | ExternalScript | All | DefaultHelp | Workflow | DscResource | Class | Configurati
    on}] [-Component <System.String[]>] -Detailed [-Functionality <System.String[]>] [-Path <System.String>] [-Role <Sy
    stem.String[]>] [<CommonParameters>]
```

+ To get “full” help for any cmdlet, which includes detailed information on associated parameters, we can use the -Full parameter:

+ 为了获得任何 cmdlet 的“完整”帮助，其中包括与之相关的参数的详细信息，我们可以使用 `-Full` 参数：

```
PS C:\> Get-Help Get-Process -Full
```

```
PS C:\Windows\system32> Get-Help Get-Process -Full

名称
    Get-Process

摘要
    Gets the processes that are running on the local computer or a remote computer.


语法
    Get-Process [[-Name] <System.String[]>] [-ComputerName <System.String[]>] [-FileVersionInfo] [-Module] [<CommonPara
    meters>]

    Get-Process [-ComputerName <System.String[]>] [-FileVersionInfo] -Id <System.Int32[]> [-Module] [<CommonParameters>
    ]

    Get-Process [-ComputerName <System.String[]>] [-FileVersionInfo] -InputObject <System.Diagnostics.Process[]> [-Modu
    le] [<CommonParameters>]

    Get-Process -Id <System.Int32[]> -IncludeUserName [<CommonParameters>]

    Get-Process [[-Name] <System.String[]>] -IncludeUserName [<CommonParameters>]

    Get-Process -IncludeUserName -InputObject <System.Diagnostics.Process[]> [<CommonParameters>]


说明
    The `Get-Process` cmdlet gets the processes on a local or remote computer.

    Without parameters, this cmdlet gets all of the processes on the local computer. You can also specify a particular
    process by process name or process ID (PID) or pass a process object through the pipeline to this cmdlet.

    By default, this cmdlet returns a process object that has detailed information about the process and supports metho
    ds that let you start and stop the process. You can also use the parameters of the `Get-Process` cmdlet to get file
     version information for the program that runs in the process and to get the modules that the process loaded.
```

+ And if we’d like to get examples on how to use a specific cmdlet, we can use the “-Examples” parameter.

+ 如果我们想要获得如何使用特定 cmdlet 的示例，我们可以使用 “-Examples” 参数。

```
PS C:\> Get-Help Get-Process -Examples
```

```
PS C:\Windows\system32> Get-Help Get-Process -Examples

名称
    Get-Process

摘要
    Gets the processes that are running on the local computer or a remote computer.


    Example 1: Get a list of all active processes on the local computer

    Get-Process

    This command gets a list of all active processes running on the local computer. For a definition of each column, se
    e the Notes (#notes)section.
    Example 2: Get all available data about one or more processes

    Get-Process winword, explorer | Format-List *

    This command gets all available data about the Winword and Explorer processes on the computer. It uses the Name par
    ameter to specify the processes, but it omits the optional parameter name. The pipeline operator (`|`) passes the d
    ata to the `Format-List` cmdlet, which displays all available properties (`*`) of the Winword and Explorer process
    objects.

    You can also identify the processes by their process IDs. For instance, `Get-Process -Id 664, 2060`.
```

+ Alternatively, if we want to get current Help pages from online for any of the cmdlets or Functions, we can simply supply the -Online parameter to our command line, and will launch a web browser to the corresponding help page:

+ 另外，如果我们想要从在线获取任何 cmdlet 或函数的当前帮助页面，我们可以简单地在命令行中提供 `-Online` 参数，这将启动网页浏览器并打开相应的帮助页面：

```
PS C:\> Get-Help Get-Help -Online
```

+ If we’d like to update our locally installed help files for PowerShell via the CLI, we can do so with the “Update-Help” cmdlet:

+ 如果我们想通过 CLI 更新本地安装的 PowerShell 帮助文件，我们可以使用 “Update-Help” cmdlet 来实现：

```
PS C:\Windows\system32> Update-Help
```

+ More information on using the “Get-Help” cmdlet can be found here: https://technet.microsoft.com/enus/library/cc764318.aspx

+ 有关使用 “Get-Help” cmdlet 的更多信息可以在此处找到：https://technet.microsoft.com/enus/library/cc764318.aspx

在 PowerShell 中，`Get-Help` cmdlet 是一个非常重要的工具，它用来获取命令、函数、cmdlet、模块以及脚本的帮助文档。这个 cmdlet 提供了关于 PowerShell 命令如何使用的详细信息，包括命令的语法、参数和示例，这些都是学习和掌握 PowerShell 不可或缺的资源。

主要特点

1. **查看命令和参数的详细信息**：你可以使用 `Get-Help` 来查找关于任何 PowerShell cmdlet 的详细信息，这包括它的参数、用法示例以及与之相关的详细描述。

2. **支持在线和离线帮助**：`Get-Help` 既可以显示本地安装的帮助文件，也可以连接到互联网获取最新的帮助内容（通过 `-Online` 参数）。

3. **查看示例**：通过使用 `-Examples` 参数，`Get-Help` 可以提供关于如何使用特定命令的实际示例，这对于学习命令的具体应用非常有帮助。

4. **获取参数的详细信息**：使用 `-Detailed` 或 `-Full` 参数可以获得关于 cmdlet 参数更完整的描述。

使用示例

- **获取基本帮助**：
  ```powershell
  Get-Help Get-Process
  ```
  这会显示关于 `Get-Process` cmdlet 的基本帮助信息。

- **获取带示例的帮助**：
  ```powershell
  Get-Help Get-Process -Examples
  ```
  这会显示 `Get-Process` 的用法示例。

- **获取完整的帮助信息**：
  ```powershell
  Get-Help Get-Process -Full
  ```
  这会显示关于 `Get-Process` 的完整帮助信息，包括所有参数和详细的说明。

- **在线查看帮助**：
  ```powershell
  Get-Help Get-Process -Online
  ```
  这会在默认的网页浏览器中打开 `Get-Process` cmdlet 的在线帮助页面。

`Get-Help` 是掌握 PowerShell 的关键工具之一，无论是新手还是经验丰富的开发者都会频繁使用它来快速了解和学习不同的 PowerShell 命令和功能。

#### 2.1.1.2 Get-Command
+ The “Get-Command” cmdlet is another very useful one.

+ It allows us to list all cmdlets, aliases, functions, workflows, filters, scripts and any applications that are available for us to use in PowerShell.

+ “Get-Command” cmdlet 也是另一个非常有用的命令。

+ 它允许我们列出在 PowerShell 中可用的所有 cmdlet、别名、函数、工作流、过滤器、脚本以及任何应用程序。

+ Running the “Get-Command” cmdlet without arguments will simply list all commands, but we can also use the -Name parameter to list any that are useful to us.

+ For instance, we can list all functions related to modification of the Windows Firewall with the following command:

```
PS C:\> Get-Command –Name *Firewall*
```

+ 运行不带参数的 “Get-Command” cmdlet 将简单地列出所有命令，但我们也可以使用 `-Name` 参数来列出对我们有用的任何命令。

+ 例如，我们可以使用以下命令列出所有与修改 Windows 防火墙相关的函数：

```
PS C:\> Get-Command –Name *Firewall*
```

```
PS C:\Windows\system32> Get-Command -Name *Firewall*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Copy-NetFirewallRule                               2.0.0.0    NetSecurity
Function        Disable-NetFirewallRule                            2.0.0.0    NetSecurity
Function        Enable-NetFirewallRule                             2.0.0.0    NetSecurity
Function        Get-NetFirewallAddressFilter                       2.0.0.0    NetSecurity
Function        Get-NetFirewallApplicationFilter                   2.0.0.0    NetSecurity
```

### References
+ About Script Blocks: https://docs.microsoft.com/enus/powershell/module/microsoft.powershell.core/about/about_script_blocks?view=powershell-6

+ PowerShell Command Line Introduction: http://radar.oreilly.com/2013/06/powershell-commandline-introduction.html

+ Using Cmdlet Help: https://technet.microsoft.com/enus/library/cc764318.aspx







