---
layout: post
title: "PowerShell Scripts"
date: "2024-06-07"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - PowerShell for Pentesters
---

# PowerShell Scripts
## 2.4 Scripts
+ Scripts are another element of our leveraging of PowerShell as an offensive tool, and most of the time, this is probably the most common way we will utilize PowerShell for most tasks.

+ PowerShell Scripts are usually identified by the “.ps1” extension, the “1” indicating not the version of PowerShell, but rather the PowerShell engine.

+ For the most part, we’ll be dealing with the .ps1 file.

+ 脚本是我们将PowerShell作为攻击工具的另一个手段，大多数情况下，这可能是我们使用PowerShell执行大多数任务的最常见方式。

+ PowerShell脚本通常以“.ps1”扩展名标识，其中的“1”不表示PowerShell的版本，而是指PowerShell引擎。

+ 大部分情况下，我们将处理.ps1文件。

+ PowerShell scripts can contain as little as a few commands to automate some tasks or be as complex as to contain parameters, script arguments, loops, functions, and anything else related to the capabilities that PowerShell offers as a scripting language.

+ Running a powershell script is as simple as calling it from the powershell console, using the (dot-backslash) .\ notation for a script in our current directory*.

```
PS C:\> .\example.ps1
```

* You may have to bypass the current execution policy (as shown earlier) before you execute the script of your choosing.

+ PowerShell脚本可以只包含几个命令来自动化一些任务，也可以复杂到包含参数、脚本参数、循环、函数以及与PowerShell作为脚本语言提供的功能相关的其他内容。

+ 运行PowerShell脚本很简单，只需从PowerShell控制台调用它，使用（点反斜杠）.\表示法来运行当前目录下的脚本。

```
PS C:\> .\example.ps1
```

* 在执行你选择的脚本之前，你可能需要绕过当前的执行策略（如前所示）。

+ A very basic example of a PowerShell script which takes a file name as an argument would be something like the following:

```powershell
Param(
	[parameter(mandatory=$true)][string]$file
)
Get-Content "$file"
```

+ The above script simply takes a file name as an argument for which it creates a variable called “$file,” and runs the “Get-Content” cmdlet on our variable.

+ 一个非常基本的PowerShell脚本示例，它接受一个文件名作为参数，可能如下所示：

```powershell
Param(
	[parameter(mandatory=$true)][string]$file
)
Get-Content "$file"
```

+ 上述脚本简单地接受一个文件名作为参数，为此它创建了一个名为“$file”的变量，并在我们的变量上运行“Get-Content”命令。

简单来说，就是读取指定名称的文件内容。

+ Now if we run this file while supplying the name of a file, in this case, “users.txt” which contains several usernames, we can see what happens:

+ 现在，如果我们运行这个文件，并提供一个文件名，这里以“users.txt”为例，该文件包含了几个用户名，我们可以看看会发生什么：

```
PS C:\tmp> .\example.ps1 .\users.txt
john
sara
adam
steve
amanda
```

+ If we run the script without arguments, PowerShell will ask us for the file, since “mandatory=$true” has been set for the parameter function in our script:

+ 如果我们在没有参数的情况下运行脚本，由于在我们的脚本中为参数函数设置了“mandatory=$true”，PowerShell会要求我们提供文件名：

+ At which point, we provide our file name, and the script works as intended, returning to us the contents of the users.txt file.

+ 此时，我们提供文件名，脚本按预期工作，返回给我们users.txt文件的内容。

```
PS C:\tmp> .\example.ps1

位于命令管道位置 1 的 cmdlet example.ps1
请为以下参数提供值:
file: users.txt
john
sara
adam
steve
amanda
```

+ Alternatively, in regard to the example on the previous slide, rather than writing a .ps1 script file, we could also just create a variable “$file” for our users.txt file, and then call the “Get-Content” script against our variable, directly from the shell:

+ 或者，关于前一张幻灯片上的示例，我们也可以不编写.ps1脚本文件，而是直接为我们的users.txt文件创建一个变量“$file”，然后直接从Shell中调用“Get-Content”命令针对我们的变量执行：

```
PS C:\Users\user\Desktop> $file=“users.txt”
PS C:\Users\user\Desktop> Get-Content $file
```

```
PS C:\tmp> $file="users.txt"
PS C:\tmp> $file
users.txt
PS C:\tmp> Get-Content $file
john
sara
adam
steve
amanda
```

### 2.4.1 Loop Statements
+ PowerShell supports several “loop statements” which we can utilize for different tasks.

+ As we saw with a previous example of the “ForEach-Object” cmdlet, we can use loop statements to iterate through files, PowerShell object collections, and even conduct port scans which we will cover in this section.

+ PowerShell支持多种“循环语句”，我们可以利用这些语句来完成不同的任务。

+ 正如我们在之前“ForEach-Object”命令的示例中看到的，我们可以使用循环语句来遍历文件、PowerShell对象集合，甚至执行端口扫描，我们将在本节中介绍这些内容。

+ A “loop” is a programming or scripting function which iterates a statement, or condition based on specific boundaries.

+ In other words, a loop will repeatedly execute code in its body until a conditional statements returns “False” or, returns no additional data.

+ “循环”是一种编程或脚本功能，它根据特定的边界重复执行一个语句或条件。

+ 换句话说，循环将反复执行其主体中的代码，直到条件语句返回“False”或不再返回额外的数据。

PowerShell allows us to use a number of loop statements for our purposes:

PowerShell允许我们使用多种循环语句来达成我们的目的：

+ for()
+ foreach()
+ while()
+ do {something} while()
+ do {something} until()

+ And as we can with mostly everything in PowerShell, we can get help on any of those statements with the “Get-Help” cmdlet:

+ 正如我们在PowerShell中几乎所有内容都能做到的那样，我们可以使用“Get-Help”命令来获取这些语句的帮助：

```
PS C:\> Get-Help about_Foreach
PS C:\> Get-Help about_For
PS C:\> Get-Help about_Do
PS C:\> Get-Help about_While
```

+ Loops are generally divided into two parts, a loop statement, and a loop body and will also contain variables as seen in the example below:

+ 循环通常分为两部分：循环语句和循环体，并且会包含变量，如下面的示例所示：

```
PS C:\> $services = Get-Service
PS C:\> foreach ($service in $services) { $service.Name }
```

Loop Statement: foreach ($service in $services)

Loop Body: { $service.Name }

+ In the first line, we’re creating a variable called “$services” which will return the Get-Service objects collection as a result of running the “Get-Service” cmdlet.

+ We then use the “foreach()” loop statement to create a new variable “$service” to contain each resulting object of the $services variable, and finally, we’re telling PowerShell to return the name of each $service with the “.Name” property in the loop body, between the {} brackets.

+ 在第一行中，我们创建了一个名为“$services”的变量，它将通过运行“Get-Service”命令返回Get-Service对象集合的结果。

+ 然后我们使用“foreach()”循环语句创建一个新的变量“$service”，用来包含$services变量的每个结果对象，最后，我们告诉PowerShell在循环体的{}括号之间，通过“.Name”属性返回每个$service的名称。

#### 2.4.1.1 ForEach-Object
+ Furthermore, we can use several built-in cmdlets for constructing loop statements, specifically the “[ForEach-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/foreach-object?view=powershell-7.4&viewFallbackFrom=powershell-6)” and “[Where-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object?view=powershell-7.4&viewFallbackFrom=powershell-6)” cmdlets.

+ The previous example could be similarly accomplished by using the “Get-Service” and “ForEach-Object” cmdlets and pipeline, as follows:

+ 此外，我们可以使用几个内置的命令来构建循环语句，特别是“[ForEach-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/foreach-object?view=powershell-7.4&viewFallbackFrom=powershell-6)”和“[Where-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object?view=powershell-7.4&viewFallbackFrom=powershell-6)”命令。

+ 前面的例子可以通过使用“Get-Service”和“ForEach-Object”命令及管道来类似完成，如下所示：

```
PS C:\> Get-Service | ForEach-Object {$_.Name}
```

##### GPT介绍的ForEach-Object
`ForEach-Object` 是 PowerShell 中的一个非常强大的命令，用于对输入对象进行循环处理。它通常与管道操作符一起使用，以对管道中传递的每个对象执行一个或多个操作。这使得 `ForEach-Object` 成为处理大量数据时非常灵活和有用的工具。

**基本用法**

基本语法如下：

```powershell
<Collection> | ForEach-Object { <ScriptBlock> }
```

这里，`<Collection>` 可以是任何产生一系列对象的表达式，`<ScriptBlock>` 是对每个对象执行的命令块。

**示例**

假设我们有一个服务列表，我们想打印每个服务的状态：

```powershell
Get-Service | ForEach-Object { Write-Output "$($_.Name) is $($_.Status)" }
```

在这个例子中，`Get-Service` 获取当前机器上所有的服务，然后 `ForEach-Object` 为每个服务执行脚本块，在这个脚本块中，我们使用 `$_` 表示当前对象（即当前的服务），并打印出服务的名称和状态。

**参数**

`ForEach-Object` 命令还支持以下常用参数：

- `-Parallel`：在 PowerShell 7 及以上版本中，这个参数允许并行执行脚本块。
- `-ThrottleLimit`：与 `-Parallel` 结合使用，限制并行执行的最大任务数。
- `-Begin`：在处理任何输入之前执行的脚本块。
- `-Process`：对每个输入对象执行的脚本块，通常是主要操作的地方。
- `-End`：在处理所有输入后执行的脚本块。

使用 `ForEach-Object`，你可以简洁地处理各种数据，从而在自动化脚本中有效地执行复杂的数据操作。

`ForEach-Object` is a powerful command in PowerShell used for iterating over input objects. It is commonly used in conjunction with the pipeline operator to perform one or more operations on each object that passes through the pipeline. This makes `ForEach-Object` an extremely flexible and useful tool when dealing with large datasets.

**Basic Usage**

The basic syntax is as follows:

```powershell
<Collection> | ForEach-Object { <ScriptBlock> }
```

Here, `<Collection>` can be any expression that produces a series of objects, and `<ScriptBlock>` is the block of commands that executes for each object.

**Example**

Suppose you have a list of services and you want to print the status of each one:

```powershell
Get-Service | ForEach-Object { Write-Output "$($_.Name) is $($_.Status)" }
```

In this example, `Get-Service` retrieves all the services on the current machine, and then `ForEach-Object` executes a script block for each service. Within the script block, `$_` represents the current object (i.e., the current service), and the script prints the name and status of each service.

**Parameters**

The `ForEach-Object` command also supports the following commonly used parameters:

- `-Parallel`: Available in PowerShell 7 and later, this parameter allows the script block to execute in parallel.
- `-ThrottleLimit`: Used with `-Parallel` to limit the maximum number of concurrent tasks.
- `-Begin`: A script block that runs before any input is processed.
- `-Process`: The script block that executes for each input object, usually where the main operations occur.
- `-End`: A script block that runs after all inputs have been processed.

Using `ForEach-Object`, you can efficiently perform complex data manipulations in your automation scripts with concise handling of various types of data.

`Where-Object` 是 PowerShell 中一个非常实用的命令，主要用于基于条件筛选和过滤对象。它通常与管道操作符结合使用，允许用户从传入的对象流中选择满足特定条件的对象。

**基本用法**

`Where-Object` 的基本语法如下：

```powershell
<Collection> | Where-Object { <Condition> }
```

这里，`<Collection>` 是任何可以产生对象序列的表达式，而 `<Condition>` 是一个布尔表达式，用来测试每个对象是否满足特定的条件。

**示例**

假设你想筛选出正在运行的服务，你可以使用以下命令：

```powershell
Get-Service | Where-Object { $_.Status -eq 'Running' }
```

在这个例子中，`Get-Service` 获取当前机器上的所有服务，`Where-Object` 则测试每个服务对象（通过特殊变量 `$_` 表示）的 `Status` 属性是否等于 'Running'。

**参数**

`Where-Object` 支持一些有用的参数来优化查询，如：

- `-Property`：允许直接指定要测试的属性名。
- `-EQ`, `-LT`, `-GT` 等：提供简化的比较操作，如 `-Property Status -EQ 'Running'`。

**高级用法**

`Where-Object` 也支持更复杂的条件，包括多个条件的组合（使用逻辑运算符如 `-and` 和 `-or`）：

```powershell
Get-Process | Where-Object { $_.WorkingSet -gt 100MB -and $_.ProcessName -like '*svc*' }
```

这个命令筛选出工作集大于100MB并且进程名称包含“svc”的进程。

通过使用 `Where-Object`，PowerShell 用户可以非常灵活地处理各种数据，使其成为数据筛选和复杂查询中的强大工具。

`Where-Object` is a highly practical command in PowerShell, primarily used for filtering and selecting objects based on conditions. It is commonly used in conjunction with the pipeline operator, allowing users to pick objects from a stream of incoming objects that meet specific criteria.

**Basic Usage**

The basic syntax for `Where-Object` is:

```powershell
<Collection> | Where-Object { <Condition> }
```

Here, `<Collection>` is any expression that produces a sequence of objects, and `<Condition>` is a Boolean expression used to test whether each object satisfies certain conditions.

**Example**

Suppose you want to filter for services that are currently running. You could use the following command:

```powershell
Get-Service | Where-Object { $_.Status -eq 'Running' }
```

In this example, `Get-Service` retrieves all services on the current machine, and `Where-Object` tests if the `Status` property of each service object (represented by the special variable `$_`) is equal to 'Running'.

**Parameters**

`Where-Object` supports several useful parameters to refine the queries, such as:

- `-Property`: Allows specifying the property name directly for testing.
- `-EQ`, `-LT`, `-GT`, etc.: Provide simplified comparison operations, like `-Property Status -EQ 'Running'`.

**Advanced Usage**

`Where-Object` also supports more complex conditions, including combinations of multiple conditions using logical operators like `-and` and `-or`:

```powershell
Get-Process | Where-Object { $_.WorkingSet -gt 100MB -and $_.ProcessName -like '*svc*' }
```

This command filters for processes whose working set is greater than 100MB and whose process name contains "svc".

By using `Where-Object`, PowerShell users can handle a variety of data flexibly, making it a powerful tool for data filtering and complex queries.

#### 2.4.1.2 Where-Object
+ The “Where-Object” cmdlet allows us to select objects within a collection based on their property values in regard to when used for a loop. In the following example, we’re using the “Get-ChildItem” cmdlet to list the contents of a “Powershell” directory, while piping that output to the “Where-Object” cmdlet with the -match parameter to only return files that contain “xls” within their name:

+ “Where-Object”命令允许我们根据对象在集合中的属性值选择对象，特别是在用于循环时。在下面的例子中，我们使用“Get-ChildItem”命令来列出“Powershell”目录的内容，同时通过管道将输出传递给“Where-Object”命令，并使用-match参数只返回文件名中包含“xls”的文件：

```
PS C:\> Get-ChildItem C:\Powershell\ | Where-Object {$_.Name -match "xls"}
```

#### 2.4.1.3 Powershell TCP Port Scanner
+ A great example of a useful loop which the uses the “foreach” statement, is a TCP Port Scanner we can create entirely via the shell as a one-liner:

+ 一个使用“foreach”语句的非常实用的循环示例是，我们可以完全通过Shell创建一个单行的TCP端口扫描器：

```powershell
$ports=(81,443);$ip="192.168.117.128"; foreach ($port in $ports) {try{$socket=New-Object System.Net.Sockets.TcpClient($ip,$port);} catch{}; if ($socket -eq $null) {echo $ip":"$port" - Closed";}else{echo $ip":"$port" - Open"; $socket = $null;}}
```

整理格式：

```powershell
$ports = (81, 443)
$ip = "192.168.117.128"
foreach ($port in $ports) {
    try {
        $socket = New-Object System.Net.Sockets.TcpClient($ip, $port)
    } catch {}
    if ($socket -eq $null) {
        echo $ip + ":" + $port + " - Closed"
    } else {
        echo $ip + ":" + $port + " - Open"
        $socket = $null
    }
}
```

+ We can just as well put the contents of the above, into a “Scan-Ports.ps1” file in this case, for easy execution:

+ 我们同样可以将上述内容放入一个名为“Scan-Ports.ps1”的文件中，以便于执行：

Define $ports and $ip variables

```powershell
$ports=(443, 81);
$ip="192.168.117.128";

foreach ($port in $ports) {try{$socket=New-Object System.Net.Sockets.TcpClient($ip,$port);}

catch{};

if ($socket -eq $null) { echo $ip":"$port" - Closed ";} else{echo $ip":"$port" - Open"; $socket =$null;}}
```

```
PS C:\Users\user\Desktop> .\Scan-Ports.ps1
```

### References
ForEach-Object: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/foreach-object?view=powershell-7.4&viewFallbackFrom=powershell-6

Where-Object: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object?view=powershell-7.4&viewFallbackFrom=powershell-6