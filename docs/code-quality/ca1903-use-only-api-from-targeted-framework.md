---
title: CA1903:仅使用目标框架中的 API
ms.date: 11/04/2016
ms.topic: reference
f1_keywords:
- UseOnlyAPIFromTargetedFramework
- CA1903
helpviewer_keywords:
- UseOnlyApiFromTargetedFramework
- CA1903
ms.assetid: efdb5cc7-bbd8-4fa7-9fff-02b91e59350e
author: gewarren
ms.author: gewarren
manager: jillfra
ms.workload:
- multiple
ms.openlocfilehash: 2e57607cdfa8790c9b9fd4e692956f7bb823981a
ms.sourcegitcommit: 12f2851c8c9bd36a6ab00bf90a020c620b364076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66744859"
---
# <a name="ca1903-use-only-api-from-targeted-framework"></a>CA1903:仅使用目标框架中的 API

|||
|-|-|
|TypeName|UseOnlyApiFromTargetedFramework|
|CheckId|CA1903|
|类别|Microsoft.Portability|
|是否重大更改|是-如果引发对外部可见成员或类型的签名。<br /><br /> 无间断-时触发方法的正文中。|

## <a name="cause"></a>原因
 将成员或类型使用一个成员或未包含与项目的目标框架的 service pack 中引入的类型。

## <a name="rule-description"></a>规则说明
 在.NET Framework 2.0 Service Pack 1 和 2，.NET Framework 3.0 Service Pack 1 和 2 和.NET Framework 3.5 Service Pack 1 中包含新成员和类型。 面向.NET Framework 的主要版本的项目可能会无意中需要这些依赖项新的 Api。 若要防止此依赖项，将触发此规则上的任何新成员和类型不包含与项目的目标框架的默认值的用法。

 **目标框架和服务包依赖关系**

|||
|-|-|
|当目标框架是|针对成员中引入的用法，触发|
|.NET Framework 2.0|.NET framework 2.0 SP1 中，.NET Framework 2.0 SP2|
|.NET Framework 3.0|.NET framework 2.0 SP1，.NET Framework 2.0 SP2，.NET Framework 3.0 SP1，.NET Framework 3.0 SP2|
|.NET Framework 3.5|.NET Framework 3.5 SP1|
|.NET Framework 4|不适用|

 若要更改项目的目标框架，请参阅[如何：面向的.NET 版本](../ide/how-to-target-a-version-of-the-dotnet-framework.md)。

## <a name="how-to-fix-violations"></a>如何解决冲突
 若要删除对该服务包的依赖关系，请删除所有新成员或类型的用法。 如果这是有意的依赖关系，禁止显示警告，或关闭此规则。

## <a name="when-to-suppress-warnings"></a>何时禁止显示警告
 如果这不是有意依赖于指定的服务包不禁止显示此规则的警告。 在此情况下，你的应用程序可能无法运行在系统上未安装此 service pack。 禁止显示警告，或者如果这是有意的依赖项将关闭此规则。

## <a name="example"></a>示例
 下面的示例演示使用类型选项仅适用于.NET 2.0 Service Pack 1 的 DateTimeOffset 的类。 此示例需要在项目属性中的目标框架下拉列表中选择了.NET Framework 2.0。

 [!code-csharp[FxCop.Portability.UseOnlyApiFromTargetedFramework#1](../code-quality/codesnippet/CSharp/ca1903-use-only-api-from-targeted-framework_1.cs)]

## <a name="example"></a>示例
 下面的示例通过替换 DateTime 类型为 DateTimeOffset 类型的用法，修复了前面所述的冲突。

 [!code-csharp[FxCop.Portability.UseOnlyApiFromTargetedFramework2#1](../code-quality/codesnippet/CSharp/ca1903-use-only-api-from-targeted-framework_2.cs)]

## <a name="see-also"></a>请参阅

- [Portability Warnings](../code-quality/portability-warnings.md)
- [Framework 目标概述](../ide/visual-studio-multi-targeting-overview.md)