---
title: 使用菜单命令创建扩展 |Microsoft Docs
ms.date: 3/16/2019
ms.topic: conceptual
helpviewer_keywords:
- write a vspackage
- vspackage
- tutorials
- visual studio package
ms.assetid: f97104c8-2bcb-45c7-a3c9-85abeda8df98
author: madskristensen
ms.author: madsk
manager: jillfra
ms.workload:
- vssdk
ms.openlocfilehash: fb18792e2d0d357bb131af6c12e97425cd72fd05
ms.sourcegitcommit: 40d612240dc5bea418cd27fdacdf85ea177e2df3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66345372"
---
# <a name="create-an-extension-with-a-menu-command"></a>使用菜单命令创建扩展

本演练演示如何创建一个扩展按钮可启动记事本的菜单命令。

## <a name="prerequisites"></a>系统必备

从 Visual Studio 2015 开始，您并不安装 Visual Studio SDK 从下载中心获得。 它是作为 Visual Studio 安装程序中的可选功能包含在内。 此外可以在以后安装 VS SDK。 有关详细信息，请参阅[安装 Visual Studio SDK](../extensibility/installing-the-visual-studio-sdk.md)。

## <a name="create-a-menu-command"></a>创建菜单命令

1. 创建一个名为的 VSIX 项目**FirstMenuCommand**。 您可以发现中的 VSIX 项目模板**新的项目**通过搜索"vsix"对话框。

2. 项目打开后，添加名为的自定义命令项模板**FirstCommand**。 在中**解决方案资源管理器**，右键单击项目节点并选择**添加** > **新项**。 在中**添加新项**对话框中，转到**Visual C#**  > **扩展性**，然后选择**自定义命令**。 在中**名称**在窗口底部字段中，将命令文件名称更改为*FirstCommand.cs*。

3. 生成项目并启动调试。

    将显示 Visual Studio 的实验实例。 有关实验实例的详细信息，请参阅[实验实例](../extensibility/the-experimental-instance.md)。

::: moniker range="vs-2017"

4. 在实验实例中，打开**工具** > **扩展和更新**窗口。 应会看到**FirstMenuCommand**此处扩展。 (如果您打开**扩展和更新**中的 Visual Studio 工作实例，不会看到**FirstMenuCommand**)。

::: moniker-end

::: moniker range=">=vs-2019"

4. 在实验实例中，打开**扩展** > **管理扩展**窗口。 应会看到**FirstMenuCommand**此处扩展。 (如果您打开**管理扩展**中的 Visual Studio 工作实例，不会看到**FirstMenuCommand**)。

::: moniker-end

现在，转到**工具**的实验实例中的菜单。 应会看到**调用 FirstCommand**命令。 此时，该命令将显示消息框，指示**FirstCommandPackage 内 FirstMenuCommand.FirstCommand.MenuItemCallback()** 。 我们将了解如何实际从下一节中的此命令启动记事本。

## <a name="change-the-menu-command-handler"></a>更改菜单命令处理程序

现在让我们更新要启动记事本的命令处理程序。

1. 停止调试并返回到 Visual Studio 的工作实例。 打开*FirstCommand.cs*文件，并添加以下 using 语句：

    ```csharp
    using System.Diagnostics;
    ```

2. 查找专用 FirstCommand 构造函数。 这是命令挂接到命令服务和命令处理程序指定的位置。 将命令处理程序的名称更改为 StartNotepad，按如下所示：

    ```csharp
    private FirstCommand(AsyncPackage package, OleMenuCommandService commandService)
    {
        this.package = package ?? throw new ArgumentNullException(nameof(package));
        commandService = commandService ?? throw new ArgumentNullException(nameof(commandService));

        CommandID menuCommandID = new CommandID(CommandSet, CommandId);
        // Change to StartNotepad handler.
        MenuCommand menuItem = new MenuCommand(this.StartNotepad, menuCommandID);
        commandService.AddCommand(menuItem);
    }
    ```

3. 删除`MenuItemCallback`方法并添加`StartNotepad`方法，只需将启动记事本:

    ```csharp
    private void StartNotepad(object sender, EventArgs e)
    {
        Process proc = new Process();
        proc.StartInfo.FileName = "notepad.exe";
        proc.Start();
    }
    ```

4. 现在试一试。当开始调试项目并单击**工具** > **调用 FirstCommand**，您应看到出现的记事本实例。

    可以使用的实例<xref:System.Diagnostics.Process>类来运行任何可执行文件，而不仅仅是记事本。 尝试将它用于`calc.exe`，例如。

## <a name="clean-up-the-experimental-environment"></a>清理实验环境

如果正在开发多个扩展，或者只要了解使用不同版本的扩展插件代码的结果，您的实验环境可能会停止工作的方式。 在这种情况下，您应该运行重置脚本。 它称为**重置 Visual Studio 实验实例**，和它作为 Visual Studio SDK 的一部分提供。 此脚本删除对你的扩展实验环境中的所有引用，以便你可以从头开始。

你可以转到此脚本在两种方式之一：

1. 从桌面中，找到**重置 Visual Studio 实验实例**。

2. 从命令行运行以下命令：

    ```xml
    <VSSDK installation>\VisualStudioIntegration\Tools\Bin\CreateExpInstance.exe /Reset /VSInstance=<version> /RootSuffix=Exp && PAUSE

    ```

## <a name="deploy-your-extension"></a>部署您的扩展插件

现在，已在运行所需的方式的工具扩展，它是时间来考虑与朋友和同事共享。 只要他们具有 Visual Studio 2015 安装十分简单。 只需是向他们发送 *.vsix*生成的文件。 （请确保在发布模式下生成它。）

您可以找到 *.vsix*文件中此扩展*FirstMenuCommand* bin 目录。 具体而言，假定您已生成的发布配置，则将为：

*\<code directory>\FirstMenuCommand\FirstMenuCommand\bin\Release\ FirstMenuCommand.vsix*

若要安装扩展，您的朋友需要关闭所有打开的 Visual Studio 实例，然后双击 *.vsix*文件中，此时会出现**VSIX 安装程序**。 将文件复制到 *%LocalAppData%\Microsoft\VisualStudio\<版本 > \Extensions*目录。

您的朋友再次将显示 Visual Studio，它们会发现中的扩展名为 FirstMenuCommand**工具** > **扩展和更新**。 请转到**扩展和更新**卸载或禁用该扩展太。

## <a name="next-steps"></a>后续步骤

本演练说明了仅执行与 Visual Studio 扩展的一小部分。 下面是一个可以在 Visual Studio 扩展中执行哪些 （合理方便地） 操作的简短列表：

1. 您可以执行许多其他操作使用简单的菜单命令：

   1. 添加自己的图标：[将图标添加到菜单命令](../extensibility/adding-icons-to-menu-commands.md)

   2. 更改菜单命令的文本：[更改菜单命令的文本](../extensibility/changing-the-text-of-a-menu-command.md)

   3. 将菜单快捷方式添加到命令：[将键盘快捷方式绑定到菜单项](../extensibility/binding-keyboard-shortcuts-to-menu-items.md)

2. 添加不同类型的命令、 菜单和工具栏：[扩展菜单和命令](../extensibility/extending-menus-and-commands.md)

3. 添加工具窗口和扩展内置的 Visual Studio 工具窗口：[扩展和自定义工具窗口](../extensibility/extending-and-customizing-tool-windows.md)

4. 将 IntelliSense、 代码建议和其他功能添加到现有的代码编辑器：[将编辑器和语言服务扩展](../extensibility/extending-the-editor-and-language-services.md)

5. 将选项和属性页和用户设置添加到您的扩展插件：[扩展属性和属性窗口](../extensibility/extending-properties-and-the-property-window.md)和[扩展用户设置和选项](../extensibility/extending-user-settings-and-options.md)

   其他类型的扩展需要更多工作，如创建新的项目类型 ([扩展项目](../extensibility/extending-projects.md))，创建新的编辑器类型 ([创建自定义编辑器和设计器](../extensibility/creating-custom-editors-and-designers.md))，或实现你独立 shell 中的扩展：[Visual Studio 独立 shell](/visualstudio/extensibility/shell/visual-studio-isolated-shell)
