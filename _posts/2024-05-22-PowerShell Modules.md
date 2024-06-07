---
layout: post
title: "PowerShell Modules"
date: "2024-05-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - PowerShell for Pentesters
---

# PowerShell Modules
## 2.3 Modules
#### GPT简介
PowerShell 模块是一种封装可重用的代码包，用于扩展 PowerShell 的功能。通过使用模块，可以将多个函数、变量和运行环境设置组合在一起，从而便于共享和分发。模块的主要目的是帮助用户轻松地组织和管理他们的代码，并允许用户将复杂的功能分解成易于管理的部分。

**模块类型**

PowerShell 中有几种类型的模块：

1. **二进制模块**：通常用C#或其他.NET语言编写，并编译为DLL文件。
2. **脚本模块**：由一个或多个PowerShell 脚本文件（.ps1）组成，可能包括函数、变量等。
3. **清单模块**：包含一个模块清单（.psd1 文件），详细描述模块的属性和功能，以及包括的文件。

**创建模块**

创建一个简单的脚本模块通常涉及以下步骤：

1. **编写函数**：创建一个或多个函数，这些函数执行模块要包括的具体任务。
2. **保存为 `.psm1` 文件**：将这些函数保存在一个 `.psm1` 文件中。这个文件就是脚本模块的主体。
3. **（可选）创建模块清单**：通过新建一个 `.psd1` 清单文件，可以定义模块的元数据，如作者、版本等信息。

**使用模块**

要在 PowerShell 中使用模块，可以使用以下命令：

- **导入模块**：使用 `Import-Module` 命令来加载模块。例如，`Import-Module -Name MyModule`。
- **查找模块**：使用 `Get-Module -ListAvailable` 查看系统中所有可用的模块。
- **自动加载模块**：PowerShell 3.0 及更高版本支持模块的自动加载，无需显式导入。

**分发和安装**

模块可以通过多种方式进行分发和安装：

- **手动复制**：将模块文件复制到目标计算机上的指定目录中。
- **PowerShell 库（PSGallery）**：通过 PowerShell Gallery 发布和安装模块，使用 `Install-Module` 命令从在线库中安装。

PowerShell 模块是自动化和脚本任务的强大工具，使得复杂的管理任务变得简单和标准化。

#### 原文
+ A [module](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7.4&viewFallbackFrom=powershell-7), in simplest terms, is a set of PowerShell functionalities grouped together in the form of a single file that will typically have a “.psm1” file extension.

+ Modules are typically comprised of several components.

+ However, not all components are necessary for the functionality of a module.

+ 从最简单的角度来说，模块是一组以单个文件形式组合在一起的 PowerShell 功能，通常具有“.psm1”文件扩展名。

+ 模块通常包含几个组件。

+ 但是，并非所有组件都对模块的功能必需。

#### GPT简介
PowerShell 模块清单是一个重要的组件，用于详细定义和配置一个模块的属性和行为。这些清单文件通常具有 `.psd1` 扩展名，并包含一系列键值对，描述模块的各种特性和要求。

**主要内容和用途**

模块清单可以包含以下类型的信息：

1. **模块基本信息**：如模块的名称、版本、作者、公司名称以及版权信息。
2. **模块需求**：如 PowerShell 版本要求、所需的 .NET Framework 版本、以及其他必需的模块。
3. **功能定义**：可以定义模块导出哪些函数、变量、别名等。
4. **文件列表**：列出模块包含的所有脚本和文件，确保加载时引用正确。
5. **环境配置**：设置如模块路径、环境变量等。

**创建模块清单**

创建模块清单的基本步骤包括：

1. **新建 PSD1 文件**：在 PowerShell 或文本编辑器中创建 `.psd1` 文件。
2. **填写信息**：根据模块的需求和功能，添加相应的键值对。
3. **保存并测试**：保存文件并在 PowerShell 中测试模块是否正常工作。

**示例**

以下是一个简单的模块清单文件示例：

```powershell
@{
    # 模块的基本信息
    ModuleVersion = '1.0.0'
    Author = 'Example Author'
    CompanyName = 'Example Company'
    Copyright = 'Copyright © 2021 by Example Company'

    # 模块依赖和环境要求
    PowerShellVersion = '5.1'
    RequiredModules = @('SomeOtherModule')

    # 导出的功能
    FunctionsToExport = @('Get-Example', 'Set-Example')
    VariablesToExport = @('Variable1', 'Variable2')
    AliasesToExport = @('AliasForGet', 'AliasForSet')

    # 包含的文件
    FileList = @('lib\scripts.ps1', 'lib\helper.ps1')

    # 模块路径和环境变量
    ModulePath = 'C:\Path\To\Module'
    EnvironmentVariables = @{ 'EXAMPLE_VAR' = 'Value' }
}
```

这个文件定义了模块的版本、作者、依赖、要导出的功能和其他配置。使用清单文件，模块的用户可以轻松了解模块的功能和要求，并确保其环境中配置正确。

A PowerShell module manifest is a critical component that explicitly defines and configures the attributes and behaviors of a module. These manifest files typically have a `.psd1` extension and contain a series of key-value pairs that describe various characteristics and requirements of the module.

**Key Content and Purpose**

A module manifest can include the following types of information:

1. **Basic Module Information**: Such as the module's name, version, author, company name, and copyright information.
2. **Module Requirements**: Including requirements for the PowerShell version, necessary .NET Framework version, and other essential modules.
3. **Functionality Definitions**: Defines which functions, variables, aliases, etc., the module exports.
4. **File List**: Lists all scripts and files included in the module to ensure they are referenced correctly upon loading.
5. **Environment Configuration**: Sets up things like module paths and environment variables.

**Creating a Module Manifest**

The basic steps to create a module manifest include:

1. **Create a PSD1 File**: Create a `.psd1` file using PowerShell or a text editor.
2. **Fill in Information**: Add the necessary key-value pairs based on the module's requirements and functionalities.
3. **Save and Test**: Save the file and test in PowerShell to ensure the module works correctly.

**Example**

Here is an example of a simple module manifest file:

```powershell
@{
    # Basic module information
    ModuleVersion = '1.0.0'
    Author = 'Example Author'
    CompanyName = 'Example Company'
    Copyright = 'Copyright © 2021 by Example Company'

    # Module dependencies and environment requirements
    PowerShellVersion = '5.1'
    RequiredModules = @('SomeOtherModule')

    # Exported functionalities
    FunctionsToExport = @('Get-Example', 'Set-Example')
    VariablesToExport = @('Variable1', 'Variable2')
    AliasesToExport = @('AliasForGet', 'AliasForSet')

    # Included files
    FileList = @('lib\scripts.ps1', 'lib\helper.ps1')

    # Module path and environment variables
    ModulePath = 'C:\Path\To\Module'
    EnvironmentVariables = @{ 'EXAMPLE_VAR' = 'Value' }
}
```

This file defines the module's version, author, dependencies, exported functionalities, and other configurations. Using a manifest file, users of the module can easily understand the module's capabilities and requirements, ensuring it is configured correctly in their environment.

#### 原文
The components that can make up a typical module are:

+ Any number of powershell scripts (.ps1) or other code files, such as a managed cmdlet assembly.

+ Additional Assemblies, Help files, or scripts.

+ A [module manifest](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-module-manifest?view=powershell-7.4&viewFallbackFrom=powershell-7) file.
  
+ A directory which is used to contain all of the above.

构成一个典型模块的组件可能包括：

+ 任意数量的 PowerShell 脚本（.ps1）或其他代码文件，如托管的 cmdlet 程序集。

+ 额外的程序集、帮助文件或脚本。

+ 一个[模块清单](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-module-manifest?view=powershell-7.4&viewFallbackFrom=powershell-7)文件。

+ 一个用于包含以上所有内容的目录。

#### GPT简介
在PowerShell中，脚本模块是一种特别的模块类型，主要由PowerShell脚本文件组成。这些模块以 `.psm1` 扩展名保存，通常包含函数、变量、别名等PowerShell元素。脚本模块提供了一种便捷的方式来打包和分发PowerShell代码，使得复用和共享功能变得简单。

**主要特征和用途**

1. **封装代码**：脚本模块允许开发者将相关的PowerShell脚本和函数集中在一个文件中，便于管理和维护。
2. **易于分享**：通过脚本模块，开发者可以轻松地与他人分享复杂的脚本和库，不需要担心环境配置和依赖问题。
3. **模块隔离**：在模块内定义的函数和变量默认情况下不会影响到全局命名空间，这有助于避免命名冲突。

**创建脚本模块**

创建脚本模块的基本步骤如下：

1. **编写脚本**：将你的PowerShell函数和变量写入一个或多个 `.ps1` 文件中。
2. **创建模块文件**：将这些 `.ps1` 文件的内容转移到一个 `.psm1` 文件。这个文件将作为模块的主文件。
3. **（可选）添加模块清单**：为模块创建一个 `.psd1` 清单文件，定义模块的元数据和配置信息，如模块的作者、版本和依赖关系。

**导入和使用脚本模块**

要使用脚本模块，需要将其导入到你的PowerShell会话中。这可以通过 `Import-Module` 命令来实现：

```powershell
Import-Module -Name Path\To\YourModule.psm1
```

一旦模块被导入，其中定义的所有函数和变量就会变得可用。

**示例**

假设你有一个名为 `Utility.psm1` 的脚本模块，其中包含了一些实用的函数：

```powershell
function Get-CurrentTime {
    return Get-Date
}

function Set-Title {
    param($title)
    $host.UI.RawUI.WindowTitle = $title
}
```

在这个例子中，`Utility.psm1` 包含了两个函数：`Get-CurrentTime` 和 `Set-Title`。通过导入这个模块，你可以在任何PowerShell脚本或命令行会话中使用这些函数。

脚本模块是PowerShell自动化和脚本编写中非常强大的工具，它们提供了一种标准化和组织代码的有效方式。

In PowerShell, script modules are a specific type of module primarily composed of PowerShell script files. These modules are saved with the `.psm1` extension and typically contain functions, variables, aliases, and other PowerShell elements. Script modules provide a convenient way to package and distribute PowerShell code, making it simple to reuse and share functionalities.

**Key Features and Uses**

1. **Code Encapsulation**: Script modules allow developers to consolidate related PowerShell scripts and functions into one file, facilitating easier management and maintenance.
2. **Ease of Sharing**: Script modules enable developers to easily share complex scripts and libraries without worrying about environmental configurations and dependencies.
3. **Module Isolation**: Functions and variables defined within a module do not affect the global namespace by default, which helps to prevent naming conflicts.

**Creating Script Modules**

The basic steps for creating a script module are as follows:

1. **Write Scripts**: Write your PowerShell functions and variables into one or more `.ps1` files.
2. **Create a Module File**: Transfer the contents of these `.ps1` files into a `.psm1` file. This file will serve as the main file for the module.
3. **(Optional) Add a Module Manifest**: Create a `.psd1` manifest file for the module, defining metadata and configuration details such as the module's author, version, and dependencies.

**Importing and Using Script Modules**

To use a script module, you need to import it into your PowerShell session. This can be accomplished using the `Import-Module` command:

```powershell
Import-Module -Name Path\To\YourModule.psm1
```

Once the module is imported, all functions and variables defined within it become available.

**Example**

Suppose you have a script module named `Utility.psm1` that contains some utility functions:

```powershell
function Get-CurrentTime {
    return Get-Date
}

function Set-Title {
    param($title)
    $host.UI.RawUI.WindowTitle = $title
}
```

In this example, `Utility.psm1` includes two functions: `Get-CurrentTime` and `Set-Title`. By importing this module, you can use these functions in any PowerShell script or command-line session.

Script modules are a powerful tool in PowerShell automation and scripting, providing an effective way to standardize and organize code.

#### GPT简介
在 PowerShell 中，二进制模块是一种特殊类型的模块，它包含编译成 .NET 程序集的代码，通常以 DLL 文件的形式存在。这类模块允许开发者利用 C#、VB.NET 或其他 .NET 支持的语言来编写更加高效、功能强大的命令行工具和功能。

**主要特征**

1. **性能优势**：二进制模块通常比纯脚本模块执行更快，因为它们是预编译的。
2. **功能丰富**：开发者可以利用 .NET 框架的全部功能，包括访问底层操作系统接口和执行复杂的数据处理。
3. **安全性**：二进制模块可以实现更加复杂的安全机制，例如权限检查和加密。

**创建二进制模块**

创建二进制模块的步骤通常包括：

1. **编写代码**：使用 C# 或其他 .NET 语言编写代码。
2. **编译成 DLL**：将代码编译成 DLL 文件。
3. **创建模块清单**：编写一个 .psd1 文件，这个文件是模块的清单，其中定义了模块的属性和输出的 cmdlets。

**导入和使用二进制模块**

要使用二进制模块，首先需要将 DLL 文件和任何相关的清单文件放在 PowerShell 可以访问的目录中。然后，可以通过 `Import-Module` 命令导入模块：

```powershell
Import-Module -Name Path\To\YourModule.dll
```

导入模块后，可以在 PowerShell 会话中使用模块中定义的 cmdlets。

**示例**

假设你有一个名为 `ExampleModule.dll` 的二进制模块，它包含一个名为 `Get-ExampleData` 的 cmdlet，可以使用如下方式导入和调用：

```powershell
Import-Module -Name ExampleModule.dll
Get-ExampleData -Parameter Value
```

二进制模块是 PowerShell 中一个非常强大的工具，特别适用于需要高性能或需要与操作系统底层交互的复杂自动化任务。它们提供了一种方法，将高级编程语言的力量直接应用于 PowerShell 环境。

In PowerShell, binary modules are a specialized type of module that contain code compiled into .NET assemblies, typically existing as DLL files. These modules allow developers to write more efficient and powerful command-line tools and functionalities using C#, VB.NET, or any other .NET-supported language.

**Key Features**

1. **Performance Advantage**: Binary modules generally perform faster than pure script modules since they are pre-compiled.
2. **Rich Functionality**: Developers can leverage the full capabilities of the .NET framework, including accessing low-level operating system interfaces and performing complex data processing.
3. **Enhanced Security**: Binary modules can implement more complex security mechanisms, such as permission checks and encryption.

**Creating Binary Modules**

The steps for creating a binary module typically include:

1. **Write Code**: Write the code using C# or another .NET language.
2. **Compile into DLL**: Compile the code into a DLL file.
3. **Create a Module Manifest**: Write a .psd1 file, which is the manifest for the module, defining the module's properties and the cmdlets it exports.

**Importing and Using Binary Modules**

To use a binary module, first place the DLL file and any associated manifest files in a directory accessible to PowerShell. Then, you can import the module using the `Import-Module` command:

```powershell
Import-Module -Name Path\To\YourModule.dll
```

Once the module is imported, you can use the cmdlets defined in the module within your PowerShell session.

**Example**

Suppose you have a binary module named `ExampleModule.dll` that includes a cmdlet named `Get-ExampleData`. You can import and call it as follows:

```powershell
Import-Module -Name ExampleModule.dll
Get-ExampleData -Parameter Value
```

Binary modules are a highly powerful tool in PowerShell, particularly suited for tasks requiring high performance or complex automation that interacts with the lower levels of the operating system. They offer a way to apply the power of advanced programming languages directly within the PowerShell environment.

#### GPT简介
在 PowerShell 中，`New-Module` cmdlet 是一个用于创建动态模块的强大工具。这些模块在内存中创建并存在，不需要保存到磁盘上的物理文件。这使得 `New-Module` 非常适合于创建临时模块或在运行时动态加载功能。

**功能和用途**

`New-Module` cmdlet 允许你直接在 PowerShell 命令行中定义模块内容，这些内容包括函数、变量和别名等。你可以将一组相关的 PowerShell 命令封装在一个模块中，然后将其作为一个整体导入到会话中。

**基本用法**

基本的 `New-Module` 命令结构如下：

```powershell
$module = New-Module -ScriptBlock {
    # 在这里定义函数和变量
    function Get-MyFunction {
        "Hello, this is a custom function in a dynamic module!"
    }
} -AsCustomObject
```

上述命令创建了一个动态模块，并将其保存到变量 `$module` 中。这里的 `-ScriptBlock` 参数包含了定义模块内容的 PowerShell 脚本块。通过 `-AsCustomObject` 参数，模块被创建为一个自定义对象，可以直接访问其内部函数。

**导入模块**

创建模块后，你可以使用 `Import-Module` cmdlet 将其导入到当前会话中：

```powershell
Import-Module -Name $module
```

一旦导入，模块中定义的所有函数和变量将可在当前会话中使用。

**实例**

让我们来看一个创建并使用动态模块的实例：

```powershell
$myModule = New-Module -ScriptBlock {
    function Get-MyDate {
        Get-Date
    }
    Export-ModuleMember -Function 'Get-MyDate'
} -AsCustomObject

# 将模块导入当前会话
Import-Module -Name $myModule

# 使用模块中的函数
Get-MyDate
```

在这个例子中，我们定义了一个名为 `Get-MyDate` 的函数，该函数简单地返回当前日期和时间。通过使用 `Export-ModuleMember`，我们明确指出了要从模块中导出哪些成员。

`New-Module` 是一种快速创建和测试新模块的方法，尤其适合于开发过程中需要临时模块的场景。

#### 原文
There are also several different types of modules:

+ [Script Modules](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7.4&viewFallbackFrom=powershell-7) (We’ll be working with these for the most part)

+ [Binary Modules](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-binary-module?view=powershell-7.4&viewFallbackFrom=powershell-7)

+ [Manifest Modules](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-module-manifest?view=powershell-7.4&viewFallbackFrom=powershell-7)

+ Dynamic Modules (Created dynamically by scripts using the “[New-Module](https://learn.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/new-module?view=powershell-7.4)” cmdlet)


还有几种不同类型的模块：

+ [脚本模块](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7.4&viewFallbackFrom=powershell-7)（我们主要会使用这些）

+ [二进制模块](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-binary-module?view=powershell-7.4&viewFallbackFrom=powershell-7)

+ [清单模块](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-module-manifest?view=powershell-7.4&viewFallbackFrom=powershell-7)

+ 动态模块（使用“[New-Module](https://learn.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/new-module?view=powershell-7.4)” cmdlet 由脚本动态创建）

### 2.3.1 Get-Module
+ Modules are typically “imported” into the current PowerShell session. To obtain a list of all currently imported modules, we can use the “Get-Module” cmdlet. In the example below, we can see all of the currently imported modules for the current PowerShell session.

+ 模块通常会被“导入”到当前的 PowerShell 会话中。要获取所有当前导入的模块的列表，我们可以使用 “Get-Module” cmdlet。在下面的示例中，我们可以看到当前 PowerShell 会话中所有已导入的模块。

```
PS C:\> Get-Module
```

```
PS C:\Windows\system32> Get-Module

ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   3.1.0.0    Microsoft.PowerShell.Utility        {Add-Member, Add-Type, Clear-Variable, Compare-Object...}
Script     2.0.0      PSReadline                          {Get-PSReadLineKeyHandler, Get-PSReadLineOption, Remove-PS...
```

+ We can also list all modules available to us for importing with the “-ListAvailable” parameter, which returns a long list of available modules.

+ 我们还可以使用“-ListAvailable”参数列出所有可供导入的模块，该参数将返回一个可用模块的长列表。

```
PS C:\> Get-Module -ListAvailable
```

```
PS C:\Windows\system32> Get-Module -ListAvailable


    目录: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Script     1.0.1      Microsoft.PowerShell.Operation.V... {Get-OperationValidation, Invoke-OperationValidation}
Binary     1.0.0.1    PackageManagement                   {Find-Package, Get-Package, Get-PackageProvider, Get-Packa...
Script     3.4.0      Pester                              {Describe, Context, It, Should...}
Script     1.0.0.1    PowerShellGet                       {Install-Module, Find-Module, Save-Module, Update-Module...}
Script     2.0.0      PSReadline                          {Get-PSReadLineKeyHandler, Set-PSReadLineKeyHandler, Remov...


    目录: C:\Windows\system32\WindowsPowerShell\v1.0\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   1.0.0.0    AppBackgroundTask                   {Disable-AppBackgroundTaskDiagnosticLog, Enable-AppBackgro...
Manifest   2.0.0.0    AppLocker                           {Get-AppLockerFileInformation, Get-AppLockerPolicy, New-Ap...
```

### 2.3.2 Import-Module
+ As we’ve mentioned, modules that we want to use, will first need to be imported into our current PowerShell session. This, can be done with the “Import-Module” cmdlet, as follows:

+ 正如我们提到的，我们想要使用的模块首先需要被导入到我们当前的 PowerShell 会话中。这可以通过使用“Import-Module” cmdlet 来完成，如下所示：

```
PS C:\> Import-Module .\module.psm1
```

```
PS C:\Windows\system32> Import-Module PSReadline
```

+ Once we import a PowerShell module, all of its various cmdlets and other components become available to us, and we can simply then execute the cmdlets that are part of the module.

+ As an example, let’s take a quick look at the popular PowerShell exploitation framework “[PowerSploit](https://github.com/PowerShellMafia/PowerSploit)”, and how we would go about importing all of its functionality into our current PowerShell session.

+ 一旦我们导入了一个 PowerShell 模块，它的所有各种 cmdlet 和其他组件都将可用于我们，然后我们可以简单地执行该模块的部分 cmdlet。

+ 作为一个例子，让我们快速看一下流行的 PowerShell 利用框架 “[PowerSploit](https://github.com/PowerShellMafia/PowerSploit)”，以及我们如何将其所有功能导入到我们当前的 PowerShell 会话中。

+ Its usage and installation is straightforward, and we should be able to get it up and running in just a few steps.

+ 它的使用和安装非常简单，我们应该能够在几个步骤内就让它运行起来。

+ First, we download the PowerSploit package to our local machine from the following location: https://github.com/PowerShellMafia/PowerSploit/archive/master.zip

+ 首先，我们从以下位置下载 PowerSploit 包到我们的本地机器：https://github.com/PowerShellMafia/PowerSploit/archive/master.zip

+ The PowerSploit modules will need to be copied into one of the module paths specified by the “$Env:PSModulePath” PowerShell environment variable. To find these paths, simply type the above into your PowerShell Console:

+ PowerSploit 模块需要被复制到由 PowerShell 环境变量 “$Env:PSModulePath” 指定的模块路径之一。要找到这些路径，只需在你的 PowerShell 控制台中输入上述命令：

```
PS C:\> $Env:PSModulePath
```

+ For our purposes, we’ll use the local user's module path, which is in:

+ 出于我们的目的，我们将使用本地用户的模块路径，位于：

```
C:\users\user\Documents\WindowsPowerShell\Modules
```

```
PS C:\Windows\system32> $Env:PSModulePath
C:\Users\Lenovo\Documents\WindowsPowerShell\Modules;C:\Program Files\WindowsPowerShell\Modules;C:\Windows\system32\WindowsPowerShell\v1.0\Modules
```

+ We’ll need to then create a “PowerSploit” folder in our chosen Modules directory, where we will copy all of the contents of the PowerSploit archive into.

+ 然后我们需要在我们选择的模块目录中创建一个“PowerSploit”文件夹，将 PowerSploit 归档的所有内容复制到其中。

```
PS C:\Windows\system32> Set-ExecutionPolicy -ExecutionPolicy Bypass

执行策略更改
执行策略可帮助你防止执行不信任的脚本。更改执行策略可能会产生安全风险，如 https:/go.microsoft.com/fwlink/?LinkID=135170
中的 about_Execution_Policies 帮助主题所述。是否要更改执行策略?
[Y] 是(Y)  [A] 全是(A)  [N] 否(N)  [L] 全否(L)  [S] 暂停(S)  [?] 帮助 (默认值为“N”): A
PS C:\Windows\system32> Import-Module PowerSploit
PS C:\Windows\system32> Get-Command -Module PowerSploit

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Add-ObjectAcl                                      3.0.0.0    PowerSploit
Alias           Convert-NameToSid                                  3.0.0.0    PowerSploit
Alias           Convert-SidToName                                  3.0.0.0    PowerSploit
Alias           Find-ForeignGroup                                  3.0.0.0    PowerSploit
Alias           Find-ForeignUser                                   3.0.0.0    PowerSploit
Alias           Find-GPOComputerAdmin                              3.0.0.0    PowerSploit
Alias           Find-GPOLocation                                   3.0.0.0    PowerSploit
Alias           Find-ManagedSecurityGroups                         3.0.0.0    PowerSploit
Alias           Get-ADObject                                       3.0.0.0    PowerSploit
Alias           Get-CachedRDPConnection                            3.0.0.0    PowerSploit
Alias           Get-CurrentUserTokenGroupSid                       3.0.0.0    PowerSploit
Alias           Get-DFSshare                                       3.0.0.0    PowerSploit
Alias           Get-DNSRecord                                      3.0.0.0    PowerSploit
Alias           Get-DNSZone                                        3.0.0.0    PowerSploit
Alias           Get-DomainPolicy                                   3.0.0.0    PowerSploit
Alias           Get-GUIDMap                                        3.0.0.0    PowerSploit
Alias           Get-IPAddress                                      3.0.0.0    PowerSploit
Alias           Get-LastLoggedOn                                   3.0.0.0    PowerSploit
Alias           Get-LoggedOnLocal                                  3.0.0.0    PowerSploit
Alias           Get-NetComputer                                    3.0.0.0    PowerSploit
Alias           Get-NetDomain                                      3.0.0.0    PowerSploit
Alias           Get-NetDomainController                            3.0.0.0    PowerSploit
Alias           Get-NetDomainTrust                                 3.0.0.0    PowerSploit
Alias           Get-NetFileServer                                  3.0.0.0    PowerSploit
Alias           Get-NetForest                                      3.0.0.0    PowerSploit
Alias           Get-NetForestCatalog                               3.0.0.0    PowerSploit
Alias           Get-NetForestDomain                                3.0.0.0    PowerSploit
Alias           Get-NetForestTrust                                 3.0.0.0    PowerSploit
Alias           Get-NetGPO                                         3.0.0.0    PowerSploit
Alias           Get-NetGPOGroup                                    3.0.0.0    PowerSploit
Alias           Get-NetGroup                                       3.0.0.0    PowerSploit
Alias           Get-NetGroupMember                                 3.0.0.0    PowerSploit
Alias           Get-NetOU                                          3.0.0.0    PowerSploit
Alias           Get-NetProcess                                     3.0.0.0    PowerSploit
Alias           Get-NetSite                                        3.0.0.0    PowerSploit
Alias           Get-NetSubnet                                      3.0.0.0    PowerSploit
Alias           Get-NetUser                                        3.0.0.0    PowerSploit
Alias           Get-ObjectAcl                                      3.0.0.0    PowerSploit
Alias           Get-Proxy                                          3.0.0.0    PowerSploit
Alias           Get-RegistryMountedDrive                           3.0.0.0    PowerSploit
Alias           Get-SiteName                                       3.0.0.0    PowerSploit
Alias           Get-UserEvent                                      3.0.0.0    PowerSploit
Alias           Invoke-ACLScanner                                  3.0.0.0    PowerSploit
Alias           Invoke-AllChecks                                   3.0.0.0    PowerSploit
Alias           Invoke-CheckLocalAdminAccess                       3.0.0.0    PowerSploit
Alias           Invoke-EnumerateLocalAdmin                         3.0.0.0    PowerSploit
Alias           Invoke-EventHunter                                 3.0.0.0    PowerSploit
Alias           Invoke-FileFinder                                  3.0.0.0    PowerSploit
Alias           Invoke-MapDomainTrust                              3.0.0.0    PowerSploit
Alias           Invoke-ProcessHunter                               3.0.0.0    PowerSploit
Alias           Invoke-ShareFinder                                 3.0.0.0    PowerSploit
Alias           Invoke-ThreadedFunction                            3.0.0.0    PowerSploit
Alias           Invoke-UserHunter                                  3.0.0.0    PowerSploit
Alias           Request-SPNTicket                                  3.0.0.0    PowerSploit
Alias           Set-ADObject                                       3.0.0.0    PowerSploit
Function        Add-Persistence                                    3.0.0.0    PowerSploit
Function        Add-ServiceDacl                                    3.0.0.0    PowerSploit
Function        Find-AVSignature                                   3.0.0.0    PowerSploit
Function        Find-InterestingFile                               3.0.0.0    PowerSploit
Function        Find-LocalAdminAccess                              3.0.0.0    PowerSploit
Function        Find-PathDLLHijack                                 3.0.0.0    PowerSploit
Function        Find-ProcessDLLHijack                              3.0.0.0    PowerSploit
Function        Get-ApplicationHost                                3.0.0.0    PowerSploit
Function        Get-GPPPassword                                    3.0.0.0    PowerSploit
Function        Get-HttpStatus                                     3.0.0.0    PowerSploit
Function        Get-Keystrokes                                     3.0.0.0    PowerSploit
Function        Get-ModifiablePath                                 3.0.0.0    PowerSploit
Function        Get-ModifiableRegistryAutoRun                      3.0.0.0    PowerSploit
Function        Get-ModifiableScheduledTaskFile                    3.0.0.0    PowerSploit
Function        Get-ModifiableService                              3.0.0.0    PowerSploit
Function        Get-ModifiableServiceFile                          3.0.0.0    PowerSploit
Function        Get-NetLocalGroup                                  3.0.0.0    PowerSploit
Function        Get-NetLoggedon                                    3.0.0.0    PowerSploit
Function        Get-NetRDPSession                                  3.0.0.0    PowerSploit
Function        Get-NetSession                                     3.0.0.0    PowerSploit
Function        Get-NetShare                                       3.0.0.0    PowerSploit
Function        Get-PathAcl                                        3.0.0.0    PowerSploit
Function        Get-RegistryAlwaysInstallElevated                  3.0.0.0    PowerSploit
Function        Get-RegistryAutoLogon                              3.0.0.0    PowerSploit
Function        Get-ServiceDetail                                  3.0.0.0    PowerSploit
Function        Get-SiteListPassword                               3.0.0.0    PowerSploit
Function        Get-System                                         3.0.0.0    PowerSploit
Function        Get-TimedScreenshot                                3.0.0.0    PowerSploit
Function        Get-UnattendedInstallFile                          3.0.0.0    PowerSploit
Function        Get-UnquotedService                                3.0.0.0    PowerSploit
Function        Get-VaultCredential                                3.0.0.0    PowerSploit
Function        Get-VolumeShadowCopy                               3.0.0.0    PowerSploit
Function        Get-WebConfig                                      3.0.0.0    PowerSploit
Function        Install-ServiceBinary                              3.0.0.0    PowerSploit
Function        Install-SSP                                        3.0.0.0    PowerSploit
Function        Invoke-CredentialInjection                         3.0.0.0    PowerSploit
Function        Invoke-DllInjection                                3.0.0.0    PowerSploit
Function        Invoke-Mimikatz                                    3.0.0.0    PowerSploit
Function        Invoke-NinjaCopy                                   3.0.0.0    PowerSploit
Function        Invoke-Portscan                                    3.0.0.0    PowerSploit
Function        Invoke-PrivescAudit                                3.0.0.0    PowerSploit
Function        Invoke-ReflectivePEInjection                       3.0.0.0    PowerSploit
Function        Invoke-ReverseDnsLookup                            3.0.0.0    PowerSploit
Function        Invoke-ServiceAbuse                                3.0.0.0    PowerSploit
Function        Invoke-Shellcode                                   3.0.0.0    PowerSploit
Function        Invoke-TokenManipulation                           3.0.0.0    PowerSploit
Function        Invoke-WmiCommand                                  3.0.0.0    PowerSploit
Function        Mount-VolumeShadowCopy                             3.0.0.0    PowerSploit
Function        New-ElevatedPersistenceOption                      3.0.0.0    PowerSploit
Function        New-UserPersistenceOption                          3.0.0.0    PowerSploit
Function        New-VolumeShadowCopy                               3.0.0.0    PowerSploit
Function        Out-CompressedDll                                  3.0.0.0    PowerSploit
Function        Out-EncodedCommand                                 3.0.0.0    PowerSploit
Function        Out-EncryptedScript                                3.0.0.0    PowerSploit
Function        Out-Minidump                                       3.0.0.0    PowerSploit
Function        Remove-VolumeShadowCopy                            3.0.0.0    PowerSploit
Function        Restore-ServiceBinary                              3.0.0.0    PowerSploit
Function        Set-CriticalProcess                                3.0.0.0    PowerSploit
Function        Set-MasterBootRecord                               3.0.0.0    PowerSploit
Function        Test-ServiceDaclPermission                         3.0.0.0    PowerSploit
Function        Write-HijackDll                                    3.0.0.0    PowerSploit
Function        Write-ServiceBinary                                3.0.0.0    PowerSploit
Function        Write-UserAddMSI                                   3.0.0.0    PowerSploit
```

Important note on PowerSploit and Antivirus:

+ Many exploitation frameworks, will be detected as “hacking tools” and other signatures by a number of Antivirus solutions. This is somewhat “normal”, it’s Antivirus just doing its job, in this case, at detecting strings within the powershell scripts as being malicious, or flagging on names of modules, etc. Either way, you can create an exclude directory for your AV software for the purpose of this lesson and download the modules into that directory for now.

关于 PowerSploit 和防病毒软件的重要说明：

+ 许多利用框架会被一些防病毒解决方案检测为“黑客工具”和其他签名。这在某种程度上是“正常”的，这只是防病毒软件在做它的工作，在这种情况下，它通过检测 PowerShell 脚本中的字符串是否恶意，或者标记模块的名称等来进行操作。无论如何，你可以为你的防病毒软件创建一个排除目录，为了这节课，暂时将模块下载到该目录中。

+ Once we’ve downloaded the PowerSploit archive, extracted it and copied all of its contents into our chosen module directory into a folder called “PowerSploit”, we can then launch a PowerShell console.

+ 一旦我们下载了 PowerSploit 压缩包，解压它并将所有内容复制到我们选择的模块目录中一个名为“PowerSploit”的文件夹后，我们就可以启动一个 PowerShell 控制台。

+ We can then import all of the PowerSploit modules into our current session with the Import-Module cmdlet, and if we run the “Get-Module” cmdlet, we can see it’s now included in our list of currently imported modules.

+ 然后我们可以使用 Import-Module cmdlet 将所有的 PowerSploit 模块导入到我们当前的会话中，如果我们运行 “Get-Module” cmdlet，我们可以看到它现在已经包含在我们当前导入的模块列表中。

```
PS C:\> Import-Module PowerSploit
```

```
PS C:\> Get-Module
```

```
PS C:\Windows\system32> Import-Module PowerSploit
PS C:\Windows\system32> Get-Module

ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   3.1.0.0    Microsoft.PowerShell.Management     {Add-Computer, Add-Content, Checkpoint-Computer, Clear-Con...
Manifest   3.0.0.0    Microsoft.PowerShell.Security       {ConvertFrom-SecureString, ConvertTo-SecureString, Get-Acl...
Manifest   3.1.0.0    Microsoft.PowerShell.Utility        {Add-Member, Add-Type, Clear-Variable, Compare-Object...}
Script     3.0.0.0    PowerSploit                         {Add-Persistence, Add-ServiceDacl, Find-AVSignature, Find-...
Script     2.0.0      PSReadline                          {Get-PSReadLineKeyHandler, Get-PSReadLineOption, Remove-PS...
```

+ To list all of the PowerSploit associated cmdlets (of which there are many), we can use the “Get-Command” cmdlet, and specify the PowerSploit module with the –Module parameter:

+ 要列出所有与 PowerSploit 相关的 cmdlet（数量很多），我们可以使用 “Get-Command” cmdlet，并使用 -Module 参数指定 PowerSploit 模块：

```
PS C:\> Get-Command -Module PowerSploit
```

+ Furthermore, there are help files for all of the modules. For help on a specific PowerSploit cmdlet, we simply run the Get-Help cmdlet, for instance, getting help on the “Write-HijackDLL” PowerSploit cmdlet:

+ 此外，所有模块都有帮助文件。要获得特定 PowerSploit cmdlet 的帮助，我们只需运行 Get-Help cmdlet，例如，获取关于 “Write-HijackDLL” PowerSploit cmdlet 的帮助：

```
PS C:\Windows\system32> Get-Help Write-HijackDLL
```

+ We will cover other modules we can use for our offensive purposes in sections that follow.

+ 在接下来的部分中，我们将介绍其他可用于我们攻击目的的模块。

### References

+ How to Write a PowerShell Binary Module: https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-binary-module?view=powershell-7.4&viewFallbackFrom=powershell-7

+ How to Write a PowerShell Script Module: https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7.4&viewFallbackFrom=powershell-7

+ How to write a PowerShell module manifest: https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-module-manifest?view=powershell-7.4&viewFallbackFrom=powershell-7

+ LinEnum: https://github.com/rebootuser/LinEnum

+ New-Module: https://learn.microsoft.com/zh-cn/powershell/module/microsoft.powershell.core/new-module?view=powershell-7.4

+ PowerSploit: https://github.com/PowerShellMafia/PowerSploit

+ PowerSploit-master.zip: https://github.com/PowerShellMafia/PowerSploit/archive/master.zip

+ Writing a Windows PowerShell Module: https://learn.microsoft.com/en-us/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7.4&viewFallbackFrom=powershell-7

