---
layout: post
title: "PowerShell Cmdlets"
date: "2024-05-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - PowerShell for Pentesters
---

# PowerShell Cmdlets
## 2.2 Cmdlets
As we’ve seen in the previous section, [cmdlets](https://learn.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7.4&viewFallbackFrom=powershell-7) (“command-lets”) are a big part of how we will leverage PowerShell for our offensive purposes, two of which we’ve already briefly covered:

+ “Get-Help”

+ “Get-Command”

+ Next, we’ll cover some of the ways we can leverage other useful cmdlets for our purposes.

正如我们在前一部分中所见，[cmdlets](https://learn.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7.4&viewFallbackFrom=powershell-7)（“命令小程序”）是我们利用PowerShell进行攻击性操作的重要组成部分，其中我们已经简要介绍了两个：

+ “Get-Help”

+ “Get-Command”

接下来，我们将介绍一些其他有用的cmdlets及其用途。

**Let’s first generally summarize what cmdlets are:**

+ Light-weight PowerShell scripts that perform a single function (Can be as small as a few lines of code).

+ Instances of [.NET Framework classes](https://learn.microsoft.com/en-us/previous-versions/gg145045(v=vs.110)?redirectedfrom=MSDN) derived from the [Cmdlet Base Class](https://learn.microsoft.com/en-us/dotnet/api/system.management.automation.cmdlet?view=powershellsdk-7.4.0&redirectedfrom=MSDN) and provide access to system functions.

+ Cmdlets are native commands in PowerShell (We can also create our own).

**cmdlets summarization continued:**

+ Typically written in a “Verb-Noun” file name format which helps us determine their function (e.g., Invoke-Command).

+ Typically used to return output to other Cmdlets to be then processed via a [pipeline](https://petri.com/understanding-the-powershell-pipeline) (|).

+ Every cmdlet has its own set of parameters which can be discovered through the Get-Help cmdlet as we’ve seen previously.

**首先，我们一般总结一下什么是cmdlets：**

+ 轻量级的PowerShell脚本，执行单一功能（可以仅有几行代码）。
  
+ 作为从[Cmdlet基类](https://learn.microsoft.com/en-us/dotnet/api/system.management.automation.cmdlet?view=powershellsdk-7.4.0&redirectedfrom=MSDN)派生的[.NET框架类](https://learn.microsoft.com/en-us/previous-versions/gg145045(v=vs.110)?redirectedfrom=MSDN)实例，cmdlets提供对系统功能的访问。

+ Cmdlets是PowerShell中的原生命令（我们也可以创建自己的cmdlets）。

**cmdlets总结继续：**

+ 通常以“动词-名词”的文件名格式编写，有助于我们确定其功能（例如，Invoke-Command）。

+ 通常用于将输出返回给其他cmdlets，然后通过[管道](https://petri.com/understanding-the-powershell-pipeline)（|）进行处理。

+ 每个cmdlet都有自己的一组参数，可以通过我们前面提到的Get-Help cmdlet来发现。

**IMPORTANT:**
+ It should be noted that most cmdlets, by default, when run without other parameters will return a limited set of information or “Columns.”

+ For example, just running the “Get-ChildItem” cmdlet without any other arguments or options, returns four columns named “Mode,” “LastWriteTime,”“Length” and “Name”.

```
PS C:\Users\Lenovo\Desktop> Get-ChildItem


    目录: C:\Users\Lenovo\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2024/5/14     17:07             23 calc.ps1
```


PowerShell的`Get-ChildItem` cmdlet用于获取文件系统目录中的文件和文件夹信息。它类似于其他操作系统中的“ls”（在Linux和macOS中）或“dir”（在Windows中）命令。以下是`Get-ChildItem`的一些常见用途和示例：

**基本用法**

```powershell
Get-ChildItem
```
此命令将列出当前目录中的所有文件和文件夹。

**指定路径**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents"
```
指定路径参数以获取特定目录中的项目。

**递归列出所有子目录和文件**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" -Recurse
```
使用`-Recurse`参数来递归列出目录中的所有文件和子目录。

**过滤结果**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" -Filter "*.txt"
```
使用`-Filter`参数仅列出特定类型的文件，例如所有文本文件（*.txt）。

**显示隐藏文件和系统文件**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" -Force
```
使用`-Force`参数包括隐藏文件和系统文件。

**获取详细信息**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" | Select-Object FullName, Length, LastWriteTime
```
通过管道（|）将输出传递给`Select-Object`，以仅显示文件的完整路径、大小和最后修改时间等详细信息。

**列出特定日期之后的文件**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" | Where-Object { $_.LastWriteTime -gt (Get-Date).AddDays(-30) }
```
使用`Where-Object`筛选出在最近30天内修改的文件。

**示例代码**

以下是一个示例脚本，用于列出当前目录中所有大小超过1MB的文件：

```powershell
Get-ChildItem -Path "." -Recurse | Where-Object { $_.Length -gt 1MB } | Select-Object FullName, Length
```

**详细解释**

- `Get-ChildItem`: 获取指定目录中的文件和文件夹。
- `-Path`: 指定要列出内容的目录路径。
- `-Recurse`: 递归列出目录及其子目录中的所有内容。
- `-Filter`: 根据特定模式过滤文件。
- `-Force`: 包括隐藏文件和系统文件。
- `Select-Object`: 选择并显示对象的特定属性。
- `Where-Object`: 根据条件过滤对象。

通过`Get-ChildItem`，你可以灵活地浏览和管理文件系统中的文件和文件夹，适应各种文件处理需求。

The `Get-ChildItem` cmdlet in PowerShell is used to retrieve information about files and directories in the file system. It is similar to the “ls” command in Linux and macOS or the “dir” command in Windows. Here are some common uses and examples of `Get-ChildItem`:

**Basic Usage**

```powershell
Get-ChildItem
```
This command lists all files and directories in the current directory.

**Specifying a Path**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents"
```
Use the `-Path` parameter to get items from a specific directory.

**Recursively List All Subdirectories and Files**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" -Recurse
```
Use the `-Recurse` parameter to list all files and subdirectories recursively.

**Filtering Results**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" -Filter "*.txt"
```
Use the `-Filter` parameter to list only specific types of files, such as all text files (*.txt).

**Showing Hidden and System Files**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" -Force
```
Use the `-Force` parameter to include hidden and system files.

**Getting Detailed Information**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" | Select-Object FullName, Length, LastWriteTime
```
Pipe (`|`) the output to `Select-Object` to display detailed information such as the full path, size, and last write time of the files.

**Listing Files Modified After a Specific Date**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" | Where-Object { $_.LastWriteTime -gt (Get-Date).AddDays(-30) }
```
Use `Where-Object` to filter files that have been modified in the last 30 days.

**Example Script**

Here is an example script to list all files larger than 1MB in the current directory:

```powershell
Get-ChildItem -Path "." -Recurse | Where-Object { $_.Length -gt 1MB } | Select-Object FullName, Length
```

**Detailed Explanation**

- `Get-ChildItem`: Retrieves files and directories from the specified path.
- `-Path`: Specifies the path to list the contents of.
- `-Recurse`: Recursively lists all contents in the directory and its subdirectories.
- `-Filter`: Filters files based on a specific pattern.
- `-Force`: Includes hidden and system files.
- `Select-Object`: Selects and displays specific properties of the objects.
- `Where-Object`: Filters objects based on a condition.

Using `Get-ChildItem`, you can flexibly browse and manage files and directories in the file system, accommodating various file handling needs.

+ But by piping the output of a cmdlet to the “Format-List” cmdlet, rather than columns and names as seen in the previous slide, we can return all named properties associated with its objects in a different list-like format,

+ 但是，通过将 cmdlet 的输出传递给 “Format-List” cmdlet，而不是像前一张幻灯片中那样按列和名称显示，我们可以以不同的列表格式返回与其对象关联的所有命名属性。

```
PS C:\Users> Get-ChildItem | Format-List *


PSPath            : Microsoft.PowerShell.Core\FileSystem::C:\Users\Lenovo
PSParentPath      : Microsoft.PowerShell.Core\FileSystem::C:\Users
PSChildName       : Lenovo
PSDrive           : C
PSProvider        : Microsoft.PowerShell.Core\FileSystem
PSIsContainer     : True
Mode              : d-----
BaseName          : Lenovo
Target            : {}
LinkType          :
Name              : Lenovo
FullName          : C:\Users\Lenovo
Parent            : Users
Exists            : True
Root              : C:\
Extension         :
CreationTime      : 2023/5/24 16:57:54
CreationTimeUtc   : 2023/5/24 8:57:54
LastAccessTime    : 2024/5/15 11:16:52
LastAccessTimeUtc : 2024/5/15 3:16:52
LastWriteTime     : 2023/5/24 17:00:17
LastWriteTimeUtc  : 2023/5/24 9:00:17
Attributes        : Directory
```

+ These names whether in list format or the default column format are important, as we can use those to filter the output of cmdlet objects for specific properties, as we’ll see shortly.

+ 无论是列表格式还是默认的列格式，这些名称都很重要，因为我们可以使用它们来过滤 cmdlet 对象的输出以获取特定属性，正如我们即将看到的那样。

`Format-List` cmdlet 在 PowerShell 中用于将输出格式化为属性列表，每个属性在新行上显示。这对于查看对象的所有属性非常有用，特别是当它们不能适应表格式时。

**基本用法**

```powershell
Get-Process | Format-List
```
此命令获取系统上的所有进程，并将输出格式化为列表，显示每个进程的所有属性。

**显示特定属性**

```powershell
Get-Process | Format-List Name, Id, CPU
```
此命令格式化输出，仅显示每个进程的 `Name`、`Id` 和 `CPU` 属性。

**使用通配符**

```powershell
Get-Process | Format-List *Memory*
```
此命令使用通配符过滤属性，显示与内存相关的所有属性。

**详细示例**

```powershell
Get-Service | Format-List Name, DisplayName, Status
```
此命令获取系统上的所有服务，并将输出格式化为列表，显示每个服务的 `Name`、`DisplayName` 和 `Status` 属性。

**与其他 Cmdlet 结合使用**

你可以将 `Format-List` 与其他 cmdlet 结合使用来格式化和显示详细信息。以下是几个示例：

**示例 1：显示详细的文件信息**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" | Format-List
```
此命令列出指定路径中的所有文件和目录，并将输出格式化为列表，显示每个项目的所有属性。

**示例 2：过滤和格式化**

```powershell
Get-Process | Where-Object { $_.CPU -gt 100 } | Format-List Name, Id, CPU
```
此命令获取 CPU 使用率大于 100 的进程，并格式化输出，仅显示 `Name`、`Id` 和 `CPU` 属性。

**常见参数解释**

- `-Property`: 指定要显示的属性。你可以使用通配符选择多个属性。
- `-Force`: 强制 cmdlet 显示隐藏或默认情况下不显示的属性。

**使用别名**

`Format-List` 也可以使用其别名 `fl` 来调用：

```powershell
Get-Process | fl Name, Id, CPU
```

**总结**

- `Format-List` 用于将输出格式化为每个属性在新行上显示的列表。
- 它在属性不能适应表格式时非常有用，可以显示详细信息。
- 你可以指定特定属性显示或使用通配符过滤属性。
- `Format-List` 可以与其他 cmdlet 结合使用来格式化和显示详细信息。

通过使用 `Format-List`，你可以确保查看工作对象的所有相关详细信息，使数据更易于理解和分析。


`Format-List` cmdlet 在 PowerShell 中用于将输出格式化为属性列表，每个属性在新行上显示。这对于查看对象的所有属性非常有用，特别是当它们不能适应表格式时。

**基本用法**

```powershell
Get-Process | Format-List
```
此命令获取系统上的所有进程，并将输出格式化为列表，显示每个进程的所有属性。

**显示特定属性**

```powershell
Get-Process | Format-List Name, Id, CPU
```
此命令格式化输出，仅显示每个进程的 `Name`、`Id` 和 `CPU` 属性。

**使用通配符**

```powershell
Get-Process | Format-List *Memory*
```
此命令使用通配符过滤属性，显示与内存相关的所有属性。

**详细示例**

```powershell
Get-Service | Format-List Name, DisplayName, Status
```
此命令获取系统上的所有服务，并将输出格式化为列表，显示每个服务的 `Name`、`DisplayName` 和 `Status` 属性。

**与其他 Cmdlet 结合使用**

你可以将 `Format-List` 与其他 cmdlet 结合使用来格式化和显示详细信息。以下是几个示例：

**示例 1：显示详细的文件信息**

```powershell
Get-ChildItem -Path "C:\Users\YourUsername\Documents" | Format-List
```
此命令列出指定路径中的所有文件和目录，并将输出格式化为列表，显示每个项目的所有属性。

**示例 2：过滤和格式化**

```powershell
Get-Process | Where-Object { $_.CPU -gt 100 } | Format-List Name, Id, CPU
```
此命令获取 CPU 使用率大于 100 的进程，并格式化输出，仅显示 `Name`、`Id` 和 `CPU` 属性。

**常见参数解释**

- `-Property`: 指定要显示的属性。你可以使用通配符选择多个属性。
- `-Force`: 强制 cmdlet 显示隐藏或默认情况下不显示的属性。

**使用别名**

`Format-List` 也可以使用其别名 `fl` 来调用：

```powershell
Get-Process | fl Name, Id, CPU
```

**总结**

- `Format-List` 用于将输出格式化为每个属性在新行上显示的列表。
- 它在属性不能适应表格式时非常有用，可以显示详细信息。
- 你可以指定特定属性显示或使用通配符过滤属性。
- `Format-List` 可以与其他 cmdlet 结合使用来格式化和显示详细信息。

通过使用 `Format-List`，你可以确保查看工作对象的所有相关详细信息，使数据更易于理解和分析。

### 2.2.1 Pipelining（管道操作）
+ The results of all cmdlet output, are usually referred to as “objects.”

+ These objects can be further processed using what is known as“[pipelining](https://petri.com/understanding-the-powershell-pipeline),” similar to how we can chain commands together in a Linux bash shell for instance with the Pipe Operator (|).

+ 所有 cmdlet 输出的结果通常被称为“对象”。

+ 这些对象可以使用所谓的“[管道操作](https://petri.com/understanding-the-powershell-pipeline)”进行进一步处理，这类似于我们在 Linux bash shell 中使用管道运算符 (|) 将命令链接在一起的方式。

+ An example of this processing of cmdlet output objects with pipelines would be something like the following:

```
PS C:\> Get-Process | Sort-Object -Unique | Select-Object ProcessName
```

+ The above returns a list of processes (Get-Process), then sorts the list (Sort-Object) with the (-Unique) parameter, and finally, selects the “ProcessName” objects (Select-Object ProcessName) and returns a unique list of process names.

+ 使用管道处理 cmdlet 输出对象的示例如下：

```
PS C:\> Get-Process | Sort-Object -Unique | Select-Object ProcessName
```

+ 上述命令首先获取进程列表（Get-Process），然后使用 `-Unique` 参数对列表进行排序（Sort-Object），最后选择“ProcessName”对象（Select-Object ProcessName），返回一个唯一的进程名称列表。

```
PS C:\> Get-Process | Sort-Object -Unique | Select-Object ProcessName

ProcessName
-----------
AggregatorHost
ApplicationFrameHost
CalculatorApp
ChsIME
conhost
csrss
ctfmon
dllhost
dwm
explorer
fontdrvhost
Idle
lsass
```

+ We can also redirect the results of our pipeline operation to a file using a standard Redirect Operator (>):

```
PS C:\> Get-Process | Sort-Object -Unique | Select-Object ProcessName > uniq_procs.txt
```

+ 我们还可以使用标准重定向运算符（>）将管道操作的结果重定向到文件：

```
PS C:\> Get-Process | Sort-Object -Unique | Select-Object ProcessName > uniq_procs.txt
```

```
PS C:\Users\Lenovo\Desktop> Get-Process | Sort-Object -Unique | Select-Object ProcessName > uniq_procs.txt
```

### 2.2.2 Useful Cmdlets & Usage
+ Let’s now examine several examples of Cmdlet usage we’ll find useful for our work.

让我们现在来看几个对我们的工作有用的 Cmdlet 使用示例。

#### 2.2.2.1 Get-Process
+ The Get-Process cmdlet will give us a listing of all processes, as we saw in a previous example.

`Get-Process` 是 PowerShell 中的一个 cmdlet，用于检索本地或远程计算机上的进程信息。这个 cmdlet 非常有用，可以帮助你监视系统运行情况或进行故障排除。

**基本用法**

- 基本命令格式为 `Get-Process`，它将列出当前计算机上所有的进程。

**指定进程**

- 你可以通过指定进程名称来获取特定的进程信息。例如，`Get-Process -Name notepad` 将返回所有名为 "notepad" 的进程的详细信息。
- 可以使用通配符来指定一组进程，例如 `Get-Process -Name chrome*` 将返回所有以 "chrome" 开头的进程。

**输出属性**

- `Get-Process` 输出多个属性，包括进程名（`ProcessName`）、ID（`Id`）、CPU 使用时间（`CPU`）、内存使用量（`WS`），等等。
- 你可以使用 `Select-Object` cmdlet 来选择输出特定的属性，例如 `Get-Process | Select-Object ProcessName, CPU`。

**远程计算机**

- `Get-Process` 还可以用来获取远程计算机上的进程信息。这需要使用 `-ComputerName` 参数，例如 `Get-Process -ComputerName server01`。
- 请注意，使用远程功能可能需要适当的网络权限和配置。

**实用示例**

1. **找到占用最多内存的进程：**
   ```powershell
   Get-Process | Sort-Object WS -Descending | Select-Object -First 10
   ```
   这个命令将列出占用内存最多的前10个进程。

2. **监视特定进程的CPU使用情况：**
   ```powershell
   Get-Process -Name notepad | Select-Object ProcessName, CPU
   ```
   这可以帮助你了解特定进程的CPU使用情况。

`Get-Process` cmdlet 是 PowerShell 中一个强大的工具，能够提供广泛的系统进程信息，非常适合于系统管理和监控任务。

`Get-Process` is a cmdlet in PowerShell used for retrieving information about processes on either local or remote computers. This cmdlet is highly useful for monitoring system operations or for troubleshooting purposes.

**Basic Usage**

- The basic command format is `Get-Process`, which lists all processes on the current computer.

**Specifying Processes**

- You can retrieve details for a specific process by specifying its name, for example, `Get-Process -Name notepad` will return details for all processes named "notepad".
- Wildcards can be used to specify a group of processes, such as `Get-Process -Name chrome*` to return all processes starting with "chrome".

**Output Properties**

- `Get-Process` outputs multiple properties including the process name (`ProcessName`), ID (`Id`), CPU time (`CPU`), memory usage (`WS`), and more.
- You can use the `Select-Object` cmdlet to select specific properties for display, like `Get-Process | Select-Object ProcessName, CPU`.

**Remote Computers**

- `Get-Process` can also be used to retrieve process information from remote computers using the `-ComputerName` parameter, for example, `Get-Process -ComputerName server01`.
- Note that using remote capabilities may require appropriate network permissions and configurations.

**Practical Examples**

1. **Finding the most memory-intensive processes:**
   ```powershell
   Get-Process | Sort-Object WS -Descending | Select-Object -First 10
   ```
   This command lists the top 10 processes consuming the most memory.

2. **Monitoring CPU usage of a specific process:**
   ```powershell
   Get-Process -Name notepad | Select-Object ProcessName, CPU
   ```
   This can help you monitor the CPU usage of a specific process.

The `Get-Process` cmdlet is a powerful tool in PowerShell that provides extensive information about system processes, making it highly suitable for system administration and monitoring tasks.

+ Running the “Get-Process” cmdlet without any arguments returns basic information as we can see, and is formatted in a table-like format, which includes column names (properties).

运行不带任何参数的“Get-Process” cmdlet 会返回基本信息，正如我们所见，信息被格式化成类似表格的格式，包括列名（属性）。

```
PS C:\Windows\system32> Get-Process

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
     98       5     1028        956       0.03   3780   0 AggregatorHost
    314      19     7036          0       0.13   7876   1 ApplicationFrameHost
    174      10     2492          0       0.05   7832   1 backgroundTaskHost
    586      44    29284          0       0.53   7848   1 CalculatorApp
    145       8     1460          0       0.02   4128   1 ChsIME
    291      16     4428          0       0.20   6448   1 ChsIME
    269      14     4392       5224       2.34   7580   1 conhost
    464      16     1784       1288       0.42    436   0 csrss
    431      21     6428        944       1.14    524   1 csrss
    826      33    11744       3956       2.47   3564   1 ctfmon
    266      15     3944       1756       0.28   2880   0 dllhost
    210      17     3236        292       0.05   3048   0 dllhost
    255      23     4976          0       0.13   6084   1 dllhost
   1074      44    60564      12704       1.86    988   1 dwm
   2106      82    58036      47784       5.55   3676   1 explorer
     36       6     2080         36       0.16    804   1 fontdrvhost
     36       5     1668          0       0.02    812   0 fontdrvhost
      0       0       60          8                 0   0 Idle
   1255      25     6916       7084       2.63    676   0 lsass
```

+ To get all of the information (properties) associated with all of the processes, we can pipe it to the “Format-List *” cmdlet and wildcard argument.
  
+ This will give us a better idea of how we can filter the data for specific properties.

```
PS C:\> Get-Process | Format-List *
```

要获取与所有进程相关的所有信息（属性），我们可以将其传输到“Format-List *” cmdlet 和通配符参数。

这将让我们更好地了解如何筛选特定属性的数据。

```
PS C:\> Get-Process | Format-List *
```

```
Name                       : winlogon
Id                         : 588
PriorityClass              : High
FileVersion                : 10.0.19041.4165 (WinBuild.160101.0800)
HandleCount                : 275
WorkingSet                 : 135168
PagedMemorySize            : 2973696
PrivateMemorySize          : 2973696
VirtualMemorySize          : 103485440
TotalProcessorTime         : 00:00:00.0937500
SI                         : 1
Handles                    : 275
VM                         : 2203421708288
WS                         : 135168
PM                         : 2973696
NPM                        : 12864
Path                       : C:\Windows\system32\winlogon.exe
Company                    : Microsoft Corporation
CPU                        : 0.09375
ProductVersion             : 10.0.19041.4165
Description                : Windows 登录应用程序
Product                    : Microsoft® Windows® Operating System
__NounName                 : Process
BasePriority               : 13
```

+ We can further extend this to get information about specific processes and paths to their executables (in this example, Chrome and Firefox), by using the “Format-List” cmdlet and also specifying the “Path” property name.

```
PS C:\> Get-Process chrome, firefox | Sort-Object -Unique | Format-List Path
```

我们可以进一步扩展这一操作，以获取特定进程及其可执行文件路径的信息（在此例中为 Chrome 和 Firefox），方法是使用“Format-List” cmdlet 并指定“Path”属性名。

```
PS C:\> Get-Process chrome, firefox | Sort-Object -Unique | Format-List Path
```

```
PS C:\Users\Lenovo> Get-Process chrome, firefox | Sort-Object -Unique | Format-List Path


Path : C:\Program Files\Google\Chrome\Application\chrome.exe

Path : C:\Program Files\Mozilla Firefox\firefox.exe
```

+ We can also append multiple property names to the Format-List cmdlet, and obtain the processes Paths, and associated PID’s (Id) for instance:

```
PS C:\> Get-Process chrome, firefox | Sort-Object -Unique | Format-List Path,Id
```

我们还可以在 Format-List cmdlet 中附加多个属性名，从而获取进程的路径和相关的 PID（Id），例如：

```
PS C:\> Get-Process chrome, firefox | Sort-Object -Unique | Format-List Path,Id
```

```
PS C:\Users\Lenovo> Get-Process chrome, firefox | Sort-Object -Unique | Format-List Path, Id


Path : C:\Program Files\Google\Chrome\Application\chrome.exe
Id   : 21224

Path : C:\Program Files\Mozilla Firefox\firefox.exe
Id   : 21228
```

#### 2.2.2.2 Get-ChildItem
+ Something that’s good to know about cmdlets is that most of them have “Aliases.” For instance, the “Get-ChildItem” cmdlet which simply lists items in a directory, can be alternatively called by issuing the “ls” command, which is an alias for the Get-ChildItem cmdlet.

+ We would get the same exact results by simply just running “Get-ChildItem.”

值得了解的一点是，大多数 cmdlet 都有“别名”。例如，“Get-ChildItem” cmdlet 用于列出目录中的项目，也可以通过使用“ls”命令来调用，这是 Get-ChildItem cmdlet 的一个别名。

通过运行“Get-ChildItem”，我们会得到完全相同的结果。

```
PS C:\Users\Lenovo> ls
PS C:\Users\Lenovo> Get-ChildItem
```

+ To find what the aliases are for a specific cmdlet, we can use the “Get-Alias” cmdlet with the “-Definition” parameter followed by a cmdlet name, like in the following example:

```
PS C:\Users> Get-Alias -Definition Get-ChildItem
```

+ As we can see, the “Get-ChildItem” cmdlet has three aliases, “dir,” “gci,” and “ls.”

要找出特定 cmdlet 的别名，我们可以使用带有“-Definition”参数的“Get-Alias” cmdlet，并跟上一个 cmdlet 名称，如下例所示：

```
PS C:\Users> Get-Alias -Definition Get-ChildItem
```

如我们所见，“Get-ChildItem” cmdlet 有三个别名，“dir”、“gci”和“ls”。

```
PS C:\Users\Lenovo> Get-Alias -Definition Get-ChildItem

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           dir -> Get-ChildItem
Alias           gci -> Get-ChildItem
Alias           ls -> Get-ChildItem
```

`Get-Alias` 是 PowerShell 中的一个 cmdlet，用于检索 cmdlet、函数、脚本或可执行文件的别名。在 PowerShell 中，别名是指用于简化命令名称的简短名称。使用 `Get-Alias` 可以帮助你快速找到替代的命令名称，从而提高命令行操作的效率。

**基本用法**

- 运行 `Get-Alias` 不带任何参数将列出当前会话中定义的所有别名。
- 你可以使用 `-Name` 参数来查找特定的别名，例如 `Get-Alias -Name dir` 会显示 `dir` 的详细信息，通常它是 `Get-ChildItem` 的别名。

**查找命令的别名**

- 使用 `-Definition` 参数可以查找指定命令的所有别名。例如，`Get-Alias -Definition Get-ChildItem` 将列出 `Get-ChildItem` 的所有别名，如 `dir`、`ls` 等。

**创建和管理别名**

- 除了检索别名，`Get-Alias` 还常用于与 `Set-Alias` cmdlet 结合使用，后者用于创建新的别名。例如，你可以创建一个简短的别名 `ll` 来代替 `Get-ChildItem`，使其行为类似于 Linux 中的 `ls -l` 命令。

**示例**

1. **列出所有别名：**
   ```powershell
   Get-Alias
   ```

2. **查找特定命令的别名：**
   ```powershell
   Get-Alias -Definition Get-ChildItem
   ```

3. **查找与特定别名相关的命令：**
   ```powershell
   Get-Alias -Name ls
   ```

`Get-Alias` cmdlet 是 PowerShell 中非常有用的工具，特别是在你需要快速找到或创建命令的简化版时。它让命令行工作变得更加直观和高效。

`Get-Alias` is a cmdlet in PowerShell used to retrieve aliases for cmdlets, functions, scripts, or executable files. In PowerShell, an alias refers to a shorter name used to simplify the name of a command. Using `Get-Alias` can help you quickly find alternative names for commands, thereby enhancing your efficiency when working with the command line.

**Basic Usage**

- Running `Get-Alias` without any parameters will list all aliases defined in the current session.
- You can use the `-Name` parameter to look up a specific alias, for example, `Get-Alias -Name dir` will display details about the alias `dir`, which is typically an alias for `Get-ChildItem`.

**Finding Aliases for Commands**

- The `-Definition` parameter can be used to find all aliases for a specified command. For instance, `Get-Alias -Definition Get-ChildItem` will list all aliases for `Get-ChildItem`, such as `dir`, `ls`, and others.

**Creating and Managing Aliases**

- Besides retrieving aliases, `Get-Alias` is also commonly used in conjunction with the `Set-Alias` cmdlet, which is used to create new aliases. For example, you can create a short alias `ll` for `Get-ChildItem` to mimic the behavior of the `ls -l` command in Linux.

**Examples**

1. **List all aliases:**
   ```powershell
   Get-Alias
   ```

2. **Find aliases for a specific command:**
   ```powershell
   Get-Alias -Definition Get-ChildItem
   ```

3. **Find the command associated with a specific alias:**
   ```powershell
   Get-Alias -Name ls
   ```

The `Get-Alias` cmdlet is an extremely useful tool in PowerShell, especially when you need to quickly find or create simplified versions of commands. It makes command line work more intuitive and efficient.

#### 2.2.2.3 Get-WmiObject
`Get-WmiObject` 是 PowerShell 中一个强大的 cmdlet，它用于获取 Windows Management Instrumentation (WMI) 对象的信息。WMI 是一种 Windows 操作系统的管理框架，允许从本地或远程计算机收集系统信息、管理设备、操作系统和其他应用程序的数据。

**基本用法**

- 基本命令 `Get-WmiObject` 需要与 `-Query` 参数或 `-Class` 参数一起使用，以指定要检索的 WMI 类。
- 例如，使用 `Get-WmiObject -Class Win32_Process` 可以列出当前系统中所有的进程。

**检索特定信息**

- 可以通过 WMI 类来检索各种类型的系统信息。例如，`Win32_BIOS` 可以获取 BIOS 相关信息，`Win32_OperatingSystem` 可以获取操作系统的详细信息。
- 你可以使用 `-Filter` 或 WMI Query Language (WQL) 语句来进一步筛选返回的数据。

**远程操作**

- `Get-WmiObject` 支持对远程计算机的查询。使用 `-ComputerName` 参数，你可以指定远程机器的名称来获取其 WMI 信息，例如 `Get-WmiObject -Class Win32_Process -ComputerName RemotePC`。

**示例**

1. **获取本地计算机的 BIOS 信息：**
   ```powershell
   Get-WmiObject -Class Win32_BIOS
   ```

2. **查询特定进程的信息：**
   ```powershell
   Get-WmiObject -Query "SELECT * FROM Win32_Process WHERE Name = 'notepad.exe'"
   ```

3. **获取远程计算机的系统信息：**
   ```powershell
   Get-WmiObject -Class Win32_OperatingSystem -ComputerName RemotePC
   ```

**注意事项**

- PowerShell 3.0 以上版本引入了 `Get-CimInstance` cmdlet，它是基于更现代的 CIM (Common Information Model) 标准，并建议用作 `Get-WmiObject` 的替代品，因为它提供了更好的网络安全性和性能。

`Get-WmiObject` cmdlet 是 PowerShell 中用于系统管理和信息收集的基本工具，尽管它在新版 PowerShell 中逐渐被 `Get-CimInstance` 取代。

`Get-WmiObject` is a powerful cmdlet in PowerShell used to retrieve information about Windows Management Instrumentation (WMI) objects. WMI is a management framework for the Windows operating system that allows for the collection of system information, management of devices, operating systems, and other applications from local or remote computers.

**Basic Usage**

- The basic command `Get-WmiObject` needs to be used with either the `-Query` parameter or the `-Class` parameter to specify the WMI class to retrieve.
- For example, using `Get-WmiObject -Class Win32_Process` will list all processes currently running on the system.

**Retrieving Specific Information**

- Various types of system information can be retrieved through WMI classes. For instance, `Win32_BIOS` retrieves BIOS information, and `Win32_OperatingSystem` retrieves detailed information about the operating system.
- You can further filter the data returned using the `-Filter` parameter or WMI Query Language (WQL) statements.

**Remote Operations**

- `Get-WmiObject` supports querying remote computers. By using the `-ComputerName` parameter, you can specify the name of a remote machine to get its WMI information, for example, `Get-WmiObject -Class Win32_Process -ComputerName RemotePC`.

**Examples**

1. **Retrieve BIOS information of the local computer:**
   ```powershell
   Get-WmiObject -Class Win32_BIOS
   ```

2. **Query information about a specific process:**
   ```powershell
   Get-WmiObject -Query "SELECT * FROM Win32_Process WHERE Name = 'notepad.exe'"
   ```

3. **Get system information from a remote computer:**
   ```powershell
   Get-WmiObject -Class Win32_OperatingSystem -ComputerName RemotePC
   ```

**Note**

- PowerShell version 3.0 and later introduced the `Get-CimInstance` cmdlet, which is based on the more modern CIM (Common Information Model) standard and is recommended as a replacement for `Get-WmiObject` because it offers better network security and performance.

`Get-WmiObject` is a fundamental tool in PowerShell for system administration and information gathering, although it is gradually being replaced by `Get-CimInstance` in newer versions of PowerShell.

+ Another alias you’ll see quite often is “select” when used in conjunction with other cmdlets in pipeline operations and is an alias for the “Select-Object” cmdlet.

+ In this example, we’re using the “Get-WmiObject” cmdlet, (used to return information about WMI objects) in conjunction with the “-class win32_operatingsystem” parameter and arguments, and then selecting (select) all (*) properties related to that WMI object class; this returns all information related to the current operating system.

```
PS C:\> Get-WmiObject -class win32_operatingsystem | select -Property *
```

你经常会看到另一个别名“select”，它在与其他 cmdlet 结合使用时用于管道操作，它是“Select-Object” cmdlet 的别名。

在这个例子中，我们使用了“Get-WmiObject” cmdlet（用于返回 WMI 对象的信息），结合“-class win32_operatingsystem”参数和参数，然后选择（select）与该 WMI 对象类相关的所有（*）属性；这将返回与当前操作系统相关的所有信息。

```
PS C:\> Get-WmiObject -class win32_operatingsystem | select -Property *
```

```
PS C:\Users\Lenovo> Get-WmiObject -class win32_operatingsystem | select -Property *


PSComputerName                            : DESKTOP-LV1FS40
Status                                    : OK
Name                                      : Microsoft Windows 10 专业版|C:\Windows|\Device\Harddisk0\Partition3
FreePhysicalMemory                        : 1256116
FreeSpaceInPagingFiles                    : 1259176
FreeVirtualMemory                         : 1566688
__GENUS                                   : 2
__CLASS                                   : Win32_OperatingSystem
__SUPERCLASS                              : CIM_OperatingSystem
__DYNASTY                                 : CIM_ManagedSystemElement
__RELPATH                                 : Win32_OperatingSystem=@
__PROPERTY_COUNT                          : 64
__DERIVATION                              : {CIM_OperatingSystem, CIM_LogicalElement, CIM_ManagedSystemElement}
__SERVER                                  : DESKTOP-LV1FS40
__NAMESPACE                               : root\cimv2
__PATH                                    : \\DESKTOP-LV1FS40\root\cimv2:Win32_OperatingSystem=@
BootDevice                                : \Device\HarddiskVolume1
BuildNumber                               : 19045
BuildType                                 : Multiprocessor Free
Caption                                   : Microsoft Windows 10 专业版
CodeSet                                   : 936
CountryCode                               : 86
CreationClassName                         : Win32_OperatingSystem
CSCreationClassName                       : Win32_ComputerSystem
```

+ Alternatively, we could use the Format-List alias “fl” with the wildcard argument, and obtain the same list of all properties for the WMI Object:

```
PS C:\> Get-WmiObject -class win32_operatingsystem | fl *
```

或者，我们也可以使用 Format-List 的别名“fl”与通配符参数，获取 WMI 对象的所有属性的相同列表：

```
PS C:\> Get-WmiObject -class win32_operatingsystem | fl *
```

+ We can use the Get-WmiObject cmdlet to obtain information regarding any WMI Class, for instance, getting a detailed list of properties for all services with the “win32_service” class:

```
PS C:\> Get-WmiObject -class win32_service |Format-List *
```

我们可以使用 Get-WmiObject cmdlet 获取任何 WMI 类的信息，例如，使用“win32_service”类获取所有服务的属性详细列表：

```
PS C:\> Get-WmiObject -class win32_service | Format-List *
```

+ And we can further extend our pipeline and filtering operation just to give us “PathName” which includes command line arguments and paths to all service executables:

```
PS C:\> Get-WmiObject -class win32_service |Sort-Object -Unique PathName | fl Pathname
```

我们还可以进一步扩展我们的管道和过滤操作，只为我们提供包含命令行参数和所有服务可执行文件路径的“PathName”：

```
PS C:\> Get-WmiObject -class win32_service | Sort-Object -Unique PathName | fl PathName
```

```
PS C:\Users\Lenovo> Get-WmiObject -class win32_service | Sort-Object -Unique PathName | fl Pathname


Pathname :

Pathname : "C:\Program Files (x86)\Microsoft\Edge\Application\124.0.2478.105\elevation_service.exe"

Pathname : "C:\Program Files (x86)\Microsoft\EdgeUpdate\MicrosoftEdgeUpdate.exe" /medsvc

Pathname : "C:\Program Files (x86)\Microsoft\EdgeUpdate\MicrosoftEdgeUpdate.exe" /svc

Pathname : "C:\Program Files\Microsoft Update Health Tools\uhssvc.exe"

Pathname : "C:\Program Files\VMware\VMware Tools\vmtoolsd.exe"

Pathname : "C:\Program Files\VMware\VMware Tools\VMware VGAuth\VGAuthService.exe"
```

#### 2.2.2.4 Export-Csv

`Export-Csv` 是 PowerShell 中的一个 cmdlet，用于将对象导出为逗号分隔值（CSV）文件。这个 cmdlet 非常有用，特别是当你需要将数据导出到可以用表格软件（如 Microsoft Excel）或其他程序查看和处理的格式时。

**基本用法**

- `Export-Csv` 将 PowerShell 对象的属性以文本形式保存到 CSV 文件中。每个对象通常表示为一行，对象的每个属性则表示为一列。
- 基本命令格式为 `Export-Csv -Path [文件路径]`，其中 `-Path` 参数指定要创建或覆盖的 CSV 文件的位置。

**参数**

- `-NoTypeInformation`：默认情况下，`Export-Csv` 会在 CSV 文件的第一行添加类型信息。使用 `-NoTypeInformation` 参数可以去除这些信息。
- `-Delimiter`：虽然默认分隔符是逗号，但你可以使用 `-Delimiter` 参数来指定不同的字符，如制表符等。
- `-Append`：如果你希望将输出添加到现有的 CSV 文件中而不是覆盖它，可以使用 `-Append` 参数。

**示例**

1. **将进程列表导出到 CSV 文件：**
   ```powershell
   Get-Process | Export-Csv -Path "processes.csv" -NoTypeInformation
   ```
   这会将当前运行的所有进程的详细信息导出到名为 "processes.csv" 的文件中，且不包含类型信息。

2. **将特定属性导出到 CSV 文件：**
   ```powershell
   Get-Process | Select-Object Name, ID, CPU | Export-Csv -Path "selected_processes.csv" -NoTypeInformation
   ```
   只选择进程的名称、ID 和 CPU 使用情况，并将这些信息导出到 CSV 文件中。

3. **向现有的 CSV 文件添加数据：**
   ```powershell
   Get-Service | Export-Csv -Path "services.csv" -Append -NoTypeInformation
   ```
   将服务列表添加到已存在的 "services.csv" 文件中，而不覆盖原有数据。

`Export-Csv` cmdlet 是一个处理数据导出任务的强大工具，可以轻松地将数据从 PowerShell 转换成通用的 CSV 格式，便于后续的数据分析和报告工作。

`Export-Csv` is a cmdlet in PowerShell used to export objects to a comma-separated values (CSV) file. This cmdlet is particularly useful when you need to export data to a format that can be viewed and manipulated with spreadsheet software like Microsoft Excel or other programs.

**Basic Usage**

- `Export-Csv` saves the properties of PowerShell objects in text form to a CSV file. Each object typically corresponds to a row, and each property of the object corresponds to a column.
- The basic command format is `Export-Csv -Path [file path]`, where the `-Path` parameter specifies the location of the CSV file to be created or overwritten.

**Parameters**

- `-NoTypeInformation`: By default, `Export-Csv` adds type information to the first line of the CSV file. Using the `-NoTypeInformation` parameter removes this information.
- `-Delimiter`: Although the default delimiter is a comma, you can use the `-Delimiter` parameter to specify a different character, such as a tab.
- `-Append`: If you want to add the output to an existing CSV file instead of overwriting it, you can use the `-Append` parameter.

**Examples**

1. **Export a list of processes to a CSV file:**
   ```powershell
   Get-Process | Export-Csv -Path "processes.csv" -NoTypeInformation
   ```
   This command exports details of all currently running processes to a file named "processes.csv" without including type information.

2. **Export specific properties to a CSV file:**
   ```powershell
   Get-Process | Select-Object Name, ID, CPU | Export-Csv -Path "selected_processes.csv" -NoTypeInformation
   ```
   Only select and export the name, ID, and CPU usage of processes to the CSV file.

3. **Add data to an existing CSV file:**
   ```powershell
   Get-Service | Export-Csv -Path "services.csv" -Append -NoTypeInformation
   ```
   Append a list of services to an existing "services.csv" file without overwriting the original data.

The `Export-Csv` cmdlet is a powerful tool for handling data export tasks, allowing you to easily convert data from PowerShell into a universally readable CSV format for further data analysis and reporting.

+ And of course, saving the information we’re gathering to a file is important as well. We can either redirect the output of the pipeline operation to a file with the (>) Redirect Operator as we saw in an earlier example or sometimes, we may need the results in a different format for processing.

+ For this, we can pipe all of the output to the “Export-Csv” cmdlet, and save the results in CSV format:

```
PS C:\> Get-WmiObject -class win32_operatingsystem | fl * | Export-Csv C:\host_info.csv
```

当然，将我们收集的信息保存到文件中也很重要。我们可以像之前的示例那样，使用重定向操作符（>）将管道操作的输出重定向到一个文件中，或者有时我们可能需要以不同的格式处理结果。

为此，我们可以将所有输出通过管道传输到“Export-Csv” cmdlet，并将结果保存为 CSV 格式：

```
PS C:\> Get-WmiObject -class win32_operatingsystem | fl * | Export-Csv C:\host_info.csv
```

#### 2.2.2.5 Exploring the Registry
在 PowerShell 中，使用 `cd` 或其别名 `Set-Location` 命令来改变当前工作目录是非常常见的操作。当你看到 `cd HKLM:\` 这样的命令时，它实际上是在操作 PowerShell 的注册表提供程序，尤其是访问 Windows 注册表的“HKEY_LOCAL_MACHINE”（HKLM）分支。

**解释 `HKLM:\`**

- `HKLM:` 是一个 PowerShell 驱动器，代表 Windows 注册表中的 `HKEY_LOCAL_MACHINE` 部分。这个注册表分支包含了系统级的配置信息，如操作系统设置、系统服务配置以及安装在系统上的所有软件的配置信息。
- 使用 `cd HKLM:\` 或 `Set-Location HKLM:\`，可以将 PowerShell 的当前路径设置为注册表的这一部分，从而允许你浏览和修改注册表项和值。

**操作注册表**

一旦进入了 `HKLM:\` 驱动器，你就可以使用类似于文件系统的命令来操作注册表：

- 使用 `Get-ChildItem`（或其别名 `dir` 或 `ls`）浏览当前注册表项下的子项和值。
- 使用 `Set-Item` 或 `New-Item` 创建或修改注册表项和值。
- 使用 `Remove-Item` 删除注册表项或值。

**示例**

1. **查看 HKLM 分支下的项**：
   ```powershell
   cd HKLM:\
   Get-ChildItem
   ```

2. **修改注册表项**：
   ```powershell
   Set-Location HKLM:\SOFTWARE\MyCompany
   Set-ItemProperty -Path . -Name "StartMode" -Value "Auto"
   ```

这些操作通常需要管理员权限，因为 HKLM 分支包含了系统级别的配置信息。修改注册表前应当非常小心，因为错误的修改可能会导致系统不稳定或者更严重的问题。

In PowerShell, using `cd` or its alias `Set-Location` to change the current working directory is a common operation. When you see a command like `cd HKLM:\`, it specifically involves navigating within PowerShell's Registry Provider, particularly accessing the "HKEY_LOCAL_MACHINE" (HKLM) branch of the Windows Registry.

**Explanation of `HKLM:\`**

- `HKLM:` represents a PowerShell drive that maps to the `HKEY_LOCAL_MACHINE` section of the Windows Registry. This registry hive contains system-level configuration information, such as operating system settings, system services configurations, and the settings for all software installed on the system.
- By using `cd HKLM:\` or `Set-Location HKLM:\`, you can set the current path in PowerShell to this part of the registry, allowing you to navigate and modify registry entries and values.

**Working with the Registry**

Once you are in the `HKLM:\` drive, you can use commands similar to those used in the filesystem to manipulate the registry:

- Use `Get-ChildItem` (or its aliases `dir` or `ls`) to browse the subkeys and values under the current registry key.
- Use `Set-Item` or `New-Item` to create or modify registry keys and values.
- Use `Remove-Item` to delete registry keys or values.

**Examples**

1. **View items under the HKLM branch**:
   ```powershell
   cd HKLM:\
   Get-ChildItem
   ```

2. **Modify a registry entry**:
   ```powershell
   Set-Location HKLM:\SOFTWARE\MyCompany
   Set-ItemProperty -Path . -Name "StartMode" -Value "Auto"
   ```

These operations typically require administrative privileges, as the HKLM branch contains system-level configurations. Care should be taken when modifying the registry, as incorrect changes can lead to system instability or more severe issues.

+ For access to Windows Registry hives, PowerShell provides a convenient method with the following command:

```
PS C:\> cd HKLM:\
```

对于访问 Windows 注册表分支，PowerShell 提供了一个便捷的方法，使用以下命令：

```
PS C:\> cd HKLM:\
```

+ We can easily navigate into areas we might be interested in with “cd,” which is the alias for “Set-Location,” and furthermore, list the contents of our current hive with “Get-ChildItem” cmdlet or “ls”:

```
PS HKLM:\> cd .\SOFTWARE\Microsoft\Windows\CurrentVersion\
PS HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\> ls
```

我们可以使用“cd”（这是“Set-Location”的别名）轻松地导航到我们可能感兴趣的区域，此外，还可以使用“Get-ChildItem” cmdlet 或“ls”列出我们当前分支的内容：

```
PS HKLM:\> cd .\SOFTWARE\Microsoft\Windows\CurrentVersion\
PS HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\> ls
```

#### 2.2.2.6 Select-String
`Select-String` 是 PowerShell 中的一个非常有用的 cmdlet，用于在文本和字符串中搜索模式（通常是字符串或正则表达式）。它类似于 Unix 或 Linux 中的 `grep` 命令，使得用户可以快速从文件或其他输入中查找匹配的行。

**基本用法**

- `Select-String` 主要用于在文件或通过管道传送的文本中查找匹配的文本行。
- 它接受一个 `-Pattern` 参数，你可以在其中指定要搜索的文本或正则表达式。
- 你可以使用 `-Path` 参数指定一个或多个文件的路径，`Select-String` 会在这些文件中搜索指定的模式。

**参数**

- **-Path**: 指定要搜索的文件的路径。
- **-Pattern**: 定义要搜索的文本模式或正则表达式。
- **-CaseSensitive**: 使搜索区分大小写。
- **-Context**: 在每个匹配项周围显示额外的行，参数接受前后行的数量。
- **-NotMatch**: 返回不匹配模式的行。

**示例**

1. **在文件中搜索特定字符串**：
   ```powershell
   Select-String -Path "C:\example.txt" -Pattern "error"
   ```
   这个命令在文件 `example.txt` 中搜索包含 "error" 的所有行。

2. **使用正则表达式搜索**：
   ```powershell
   Get-Content "C:\log.txt" | Select-String -Pattern "^ERROR"
   ```
   这将筛选出所有以 "ERROR" 开头的行。

3. **搜索多个文件**：
   ```powershell
   Get-ChildItem -Path "C:\Logs\" -Filter "*.log" | Select-String -Pattern "failed" -CaseSensitive
   ```
   在 `C:\Logs\` 目录下的所有 `.log` 文件中区分大小写地搜索包含 "failed" 的行。

4. **显示匹配项的上下文**：
   ```powershell
   Select-String -Path "C:\example.txt" -Pattern "critical" -Context 2,2
   ```
   显示 "critical" 匹配项前后各两行的内容。

`Select-String` 是一个强大的文本搜索工具，它通过提供灵活的搜索功能来帮助用户在大量文本中快速定位信息。它的使用范围广泛，从简单的日志文件搜索到复杂的数据分析都非常实用。

+ The Select-String cmdlet along with the “-Path” and “-Pattern” arguments is yet another useful PowerShell command we can use to scour the system for files containing certain strings.

+ In the example below, we search for files of a .txt extension within a user's “Documents” directory, containing the string “pass*” in their contents:

```
PS C:\> Select-String -Path C:\users\user\Documents\*.txt -Pattern pass*
```

`Select-String` cmdlet 结合 `-Path` 和 `-Pattern` 参数是我们可以用来在系统中搜索包含特定字符串的文件的又一个有用的 PowerShell 命令。

在下面的例子中，我们在用户的“Documents”目录下搜索扩展名为 .txt 的文件，这些文件的内容中包含字符串“pass*”：

```
PS C:\> Select-String -Path C:\users\user\Documents\*.txt -Pattern pass*
```

#### 2.2.2.7 Get-Content
`Get-Content` 是 PowerShell 中的一个基础 cmdlet，用于读取文件内容。这个命令能够显示文件的文本内容，非常适用于查看大文件、读取日志文件或处理文本数据。

**基本用法**

- 基本命令 `Get-Content` 后跟文件的路径，可以显示该文件的内容。例如，使用 `Get-Content C:\example.txt` 可以读取并显示 `example.txt` 文件的内容。
- 默认情况下，`Get-Content` 一次读取文件的所有内容并将其显示在控制台上。对于非常大的文件，这可能会导致性能问题。

**参数**

- **-Path**：指定要读取的文件的路径。
- **-Raw**：通常，`Get-Content` 按行读取文件。使用 `-Raw` 参数会使 cmdlet 读取整个文件作为一个单独的、连续的字符串，这对于需要保持文件格式的场景非常有用。
- **-Encoding**：指定文件的编码。默认为 UTF-8，但可以设置为 ASCII、Unicode 等。
- **-TotalCount**：限制读取的行数。例如，使用 `-TotalCount 10` 可以只读取文件的前10行。
- **-Tail**：该参数用于显示文件的最后几行，类似于 Unix/Linux 中的 `tail` 命令。

**示例**

1. **读取文件的内容**：
   ```powershell
   Get-Content -Path "C:\example.txt"
   ```

2. **读取文件的前 5 行**：
   ```powershell
   Get-Content -Path "C:\example.log" -TotalCount 5
   ```

3. **读取文件的最后 3 行**：
   ```powershell
   Get-Content -Path "C:\example.log" -Tail 3
   ```

4. **以单个字符串读取整个文件**：
   ```powershell
   Get-Content -Path "C:\example.txt" -Raw
   ```

`Get-Content` 是 PowerShell 中处理文件内容的基本工具，它的灵活性使得它非常适合进行文件内容查看和数据处理任务。

`Get-Content` is a fundamental cmdlet in PowerShell used for reading the contents of files. This command is ideal for viewing large files, reading log files, or processing text data.

**Basic Usage**

- The basic command `Get-Content` followed by the path of a file will display its contents. For example, using `Get-Content C:\example.txt` will read and display the contents of the file `example.txt`.
- By default, `Get-Content` reads all content of the file at once and displays it on the console. This can cause performance issues with very large files.

**Parameters**

- **-Path**: Specifies the path of the file to be read.
- **-Raw**: Normally, `Get-Content` reads files line by line. The `-Raw` parameter makes the cmdlet read the entire file as a single, continuous string, which is useful in scenarios where you need to preserve the format of the file.
- **-Encoding**: Specifies the encoding of the file. The default is UTF-8, but it can be set to ASCII, Unicode, etc.
- **-TotalCount**: Limits the number of lines read. For example, using `-TotalCount 10` will read only the first 10 lines of the file.
- **-Tail**: This parameter is used to display the last few lines of the file, similar to the `tail` command in Unix/Linux.

**Examples**

1. **Read the contents of a file**:
   ```powershell
   Get-Content -Path "C:\example.txt"
   ```

2. **Read the first 5 lines of a file**:
   ```powershell
   Get-Content -Path "C:\example.log" -TotalCount 5
   ```

3. **Read the last 3 lines of a file**:
   ```powershell
   Get-Content -Path "C:\example.log" -Tail 3
   ```

4. **Read the entire file as a single string**:
   ```powershell
   Get-Content -Path "C:\example.txt" -Raw
   ```

`Get-Content` is a basic tool in PowerShell for handling file content, and its versatility makes it highly suitable for tasks involving file content viewing and data processing.


+ We can then use the “Get-Content” cmdlet to display the full contents of the “passwords.txt” file.

```
PS C:\> Get-Content C:\Users\user\Documents\passwords.txt
```

然后我们可以使用 `Get-Content` cmdlet 显示 “passwords.txt” 文件的全部内容。

```
PS C:\> Get-Content C:\Users\user\Documents\passwords.txt
```

+ Alternatively, we can obtain the same results by using the “Get-ChildItem” cmdlet alias with the recurse parameter (ls -r) which lists files within a directory recursively, then, search for files types of .txt with the “-File” parameter. We’ll then pipe that to the “ForEach-Object” alias which is (%) and a script block {} that searches for the string “pass*” in all files in the path specified with the alias for the “Select-String” cmdlet (sls):

+ We will learn more about how we can use the “ForEach-Object” cmdlet a bit later for certain tasks.

* Alias for “ForEach-Object” cmdlet (%)
* Alias for “Select-String” cmdlet (sls)
* Variable for current value in the pipeline ($_)

```
PS C:\> ls -r C:\users\user\Documents -File *.txt | % {sls -Path $_ -Pattern pass* }
```

或者，我们可以通过使用 “Get-ChildItem” cmdlet 的别名以及递归参数（ls -r），这个命令可以递归地列出目录中的文件，然后，使用 “-File” 参数搜索 .txt 类型的文件。接着，我们将其传递给 “ForEach-Object” 的别名（%）和一个脚本块 {}，在该脚本块中使用 “Select-String” cmdlet 的别名（sls）搜索指定路径中所有文件的字符串 “pass*”：

+ 我们稍后将进一步学习如何使用 “ForEach-Object” cmdlet 来执行特定任务。

* “ForEach-Object” cmdlet 的别名（%）
* “Select-String” cmdlet 的别名（sls）
* 管道中当前值的变量（$_）

```
PS C:\> ls -r C:\users\user\Documents -File *.txt | % {sls -Path $_ -Pattern pass* }
```

#### 2.2.2.8 Get-Service
`Get-Service` 是 PowerShell 中的一个 cmdlet，用于检索当前系统上运行的服务的状态和属性。这个命令允许用户查看服务是否正在运行、已停止或处于其他任何状态，这对于系统管理和故障诊断非常有用。

**基本用法**

- 运行 `Get-Service` 将列出所有本地计算机上的服务及其当前状态。
- 可以使用 `-Name` 参数指定一个或多个服务的名称来检索特定服务的详细信息。例如，`Get-Service -Name "wuauserv"` 将显示 Windows 更新服务的状态。

**参数**

- **-Name**: 指定要检索的服务名称。
- **-DisplayName**: 根据服务的显示名称进行搜索。
- **-DependentServices**: 显示依赖指定服务的所有服务。
- **-RequiredServices**: 显示指定服务所依赖的所有服务。

**示例**

1. **列出所有服务**：
   ```powershell
   Get-Service
   ```

2. **获取特定服务的状态**：
   ```powershell
   Get-Service -Name "bits"
   ```
   这个命令将显示后台智能传输服务（BITS）的状态。

3. **列出服务及其依赖项**：
   ```powershell
   Get-Service -Name "wuauserv" -DependentServices
   ```
   这将显示 Windows 更新服务及其依赖的服务。

4. **搜索具有特定显示名称的服务**：
   ```powershell
   Get-Service -DisplayName "Remote*"
   ```
   搜索所有显示名称以 "Remote" 开头的服务。

`Get-Service` cmdlet 是 PowerShell 中非常实用的工具，特别适合于需要快速查看或管理系统服务状态的场合。通过这个命令，用户可以轻松监控、启动或停止服务，以及进行更复杂的服务管理任务。

`Get-Service` is a cmdlet in PowerShell used to retrieve the status and properties of services running on the current system. This command allows users to view whether services are running, stopped, or in any other state, which is very useful for system administration and troubleshooting.

**Basic Usage**

- Running `Get-Service` will list all services on the local computer along with their current status.
- You can use the `-Name` parameter to specify one or more service names to retrieve detailed information about specific services. For example, `Get-Service -Name "wuauserv"` will display the status of the Windows Update service.

**Parameters**

- **-Name**: Specifies the name of the service to retrieve.
- **-DisplayName**: Searches based on the service's display name.
- **-DependentServices**: Displays all services that depend on the specified service.
- **-RequiredServices**: Displays all services that the specified service depends on.

**Examples**

1. **List all services**:
   ```powershell
   Get-Service
   ```

2. **Get the status of a specific service**:
   ```powershell
   Get-Service -Name "bits"
   ```
   This command will show the status of the Background Intelligent Transfer Service (BITS).

3. **List a service and its dependencies**:
   ```powershell
   Get-Service -Name "wuauserv" -DependentServices
   ```
   This displays the Windows Update service and all services that depend on it.

4. **Search for services with a specific display name**:
   ```powershell
   Get-Service -DisplayName "Remote*"
   ```
   Searches for all services whose display name starts with "Remote".

The `Get-Service` cmdlet is an extremely useful tool in PowerShell, especially suited for scenarios requiring quick inspection or management of system service statuses. With this command, users can easily monitor, start, or stop services, as well as perform more complex service management tasks.

+ The “Get-Service” cmdlet will get us information regarding currently installed services and can be useful in the case we can identify a service which might be vulnerable to a privilege escalation exploit.

“Get-Service” cmdlet 可以帮助我们获取有关当前安装的服务的信息，在我们需要识别可能容易受到权限提升漏洞攻击的服务时，这一点非常有用。

+ Running it without parameters or arguments simply returns a three column list of all services.

```
PS C:\> Get-Service
```

不带参数或参数运行它会简单地返回所有服务的三列列表。

```
PS C:\> Get-Service
```

+ We can extend those results, as we’ve seen before, with the “Sort-Object” cmdlet. In this example, all services starting with “s*” in descending order and sorting by the “Status” property.

```
PS C:\> Get-Service “s*” | Sort-Object Status -Descending
```

正如我们之前看到的，我们可以使用“Sort-Object” cmdlet 来扩展这些结果。在这个例子中，所有以“s*”开头的服务按“Status”属性降序排序。

```
PS C:\> Get-Service “s*” | Sort-Object Status -Descending
```

```
PS C:\Users\Lenovo\Desktop> Get-Service "s*" | Sort-Object Status -Descending

Status   Name               DisplayName
------   ----               -----------
Running  SystemEventsBroker System Events Broker
Running  ShellHWDetection   Shell Hardware Detection
Running  SENS               System Event Notification Service
Running  SgrmBroker         System Guard 运行时监视代理
Running  Spooler            Print Spooler
Running  StorSvc            Storage Service
Running  SysMain            SysMain
Running  SSDPSRV            SSDP Discovery
Running  StateRepository    State Repository Service
Running  SecurityHealthS... Windows 安全中心服务
```

### Cmdlets Conclusion（Cmdlets 结论）
+ As we can see, there are many ways and variations of using cmdlets, pipelines and aliases we can leverage to conduct tasks that are relevant to our objectives, and different ways to craft commands we can use to achieve similar results.

正如我们所看到的，有许多使用 cmdlets、管道和别名的方法和变体，我们可以利用它们来执行与我们的目标相关的任务，以及不同的编写命令的方法来达到类似的结果。

### References
+ Cmdlet Class: https://msdn.microsoft.com/enus/library/system.management.automation.cmdlet(v=vs.85).aspx

+ Cmdlets: https://docs.microsoft.com/enus/powershell/scripting/developer/cmdlet/cmdletoverview?view=powershell-7

+ LinEnum: https://github.com/rebootuser/LinEnum

+ .NET Framework Class Library: https://msdn.microsoft.com/enus/library/gg145045(v=vs.110).aspx

+ Understanding the PowerShell Pipeline: https://www.petri.com/understanding-the-powershell-pipeline

+ Windows PowerShell: The Many Ways to a Custom Object: https://technet.microsoft.com/en-us/library/hh750381.aspx



