---
title: Настройка компоновщика для Blazor
author: guardrex
description: Сведения о том, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: host-and-deploy/razor-components/configure-linker
ms.openlocfilehash: c3c38ec2509344cc02f3895d5d0c2d35059d1d8e
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668067"
---
# <a name="configure-the-linker-for-blazor"></a><span data-ttu-id="b6ec2-103">Настройка компоновщика для Blazor</span><span class="sxs-lookup"><span data-stu-id="b6ec2-103">Configure the Linker for Blazor</span></span>

<span data-ttu-id="b6ec2-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="b6ec2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="b6ec2-105">Blazor выполняет компоновку для [промежуточного языка (IL)](/dotnet/standard/managed-code#intermediate-language--execution) при каждой сборке в режиме выпуска, чтобы удалить ненужный код из выходных сборок.</span><span class="sxs-lookup"><span data-stu-id="b6ec2-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during each Release mode build to remove unnecessary IL from the output assemblies.</span></span>

<span data-ttu-id="b6ec2-106">Управлять компоновкой сборок можно одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="b6ec2-106">You can control assembly linking with either of the following approaches:</span></span>

* <span data-ttu-id="b6ec2-107">отключить связывание глобально с помощью свойства MSBuild;</span><span class="sxs-lookup"><span data-stu-id="b6ec2-107">Disable linking globally with an MSBuild property.</span></span>
* <span data-ttu-id="b6ec2-108">управлять компоновкой каждой сборки с помощью файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="b6ec2-108">Control linking on a per-assembly basis with a configuration file.</span></span>

## <a name="disable-linking-with-an-msbuild-property"></a><span data-ttu-id="b6ec2-109">Отключение компоновки с помощью свойства MSBuild</span><span class="sxs-lookup"><span data-stu-id="b6ec2-109">Disable linking with an MSBuild property</span></span>

<span data-ttu-id="b6ec2-110">Компоновка включена по умолчанию в режиме выпуска при сборке приложения, включая публикацию.</span><span class="sxs-lookup"><span data-stu-id="b6ec2-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="b6ec2-111">Чтобы отключить компоновку для всех сборок, для свойства MSBuild `<BlazorLinkOnBuild>` задайте значение `false` в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="b6ec2-111">To disable linking for all assemblies, set the `<BlazorLinkOnBuild>` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="b6ec2-112">Управление компоновкой с помощью файла конфигурации</span><span class="sxs-lookup"><span data-stu-id="b6ec2-112">Control linking with a configuration file</span></span>

<span data-ttu-id="b6ec2-113">Вы можете управлять компоновкой каждой сборки. Для этого нужно предоставить XML-файл конфигурации и указать его как элемент MSBuild в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="b6ec2-113">Linking can be controlled on a per-assembly basis by providing an XML configuration file and specifying the file as an MSBuild item in the project file.</span></span>

<span data-ttu-id="b6ec2-114">Ниже приведен пример файла конфигурации (*Linker.xml*):</span><span class="sxs-lookup"><span data-stu-id="b6ec2-114">The following is an example configuration file (*Linker.xml*):</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/aspnet/Blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="b6ec2-115">Дополнительные сведения о формате файла конфигурации см. в разделе [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/linker/README.md#syntax-of-xml-descriptor) (Компоновщик IL. Синтаксис дескриптора XML).</span><span class="sxs-lookup"><span data-stu-id="b6ec2-115">To learn more about the file format for the configuration file, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/linker/README.md#syntax-of-xml-descriptor).</span></span>

<span data-ttu-id="b6ec2-116">Укажите файл конфигурации в файле проекта с помощью элемента `BlazorLinkerDescriptor`:</span><span class="sxs-lookup"><span data-stu-id="b6ec2-116">Specify the configuration file in the project file with the `BlazorLinkerDescriptor` item:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```