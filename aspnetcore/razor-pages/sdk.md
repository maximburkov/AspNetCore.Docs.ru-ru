---
title: Пакет SDK для Razor в ASP.NET Core
author: Rick-Anderson
description: Сведения о применении в ASP.NET Core функции Razor Pages, которая делает создание кодов сценариев для страниц проще и эффективнее по сравнению с MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809137"
---
# <a name="aspnet-core-razor-sdk"></a>Пакет SDK для Razor в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

## <a name="overview"></a>Обзор

[!INCLUDE[](~/includes/2.1-SDK.md)] Включает в себя `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK). Пакет SDK для Razor:

::: moniker range=">= aspnetcore-3.0"

* Требуется для сборки, упаковки и публикации проектов, содержащих файлы [Razor](xref:mvc/views/razor) , для ASP.NET Core проектов на основе MVC или [блазор](xref:blazor/index) .
* Включает набор предопределенных целевых объектов, свойств и элементов, позволяющих настраивать компиляцию файлов Razor ( *. cshtml* или *. Razor*).

Пакет SDK для Razor включает `Content` элементы с `Include`ными атрибутами, заданными `**\*.cshtml` и `**\*.razor` шаблонами глобализации. Публикуются соответствующие файлы.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Стандартизирует процесс создания, упаковки и публикации проектов, содержащих файлы [Razor](xref:mvc/views/razor), для проектов на основе MVC ASP.NET.
* Включает набор предопределенных целевых объектов, свойств и элементов, которые позволяют настраивать параметры компиляции файлов Razor.

Пакет SDK для Razor включает элемент `Content` с атрибутом `Include`, для которого задан шаблон `**\*.cshtml` глобализации. Публикуются соответствующие файлы.

::: moniker-end

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Использование пакета SDK для Razor

Большинство веб-приложений не требуется явно ссылаться на пакет SDK Razor.

::: moniker range=">= aspnetcore-3.0"

Чтобы использовать пакет SDK для Razor для построения библиотек классов, содержащих представления Razor или Razor Pages, мы рекомендуем начать с шаблона проекта РКЛ для библиотеки классов Razor. Для РКЛ, который используется для создания файлов Блазор ( *. Razor*), минимально требуется ссылка на пакет [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . РКЛ, который используется для построения представлений Razor или страниц (*CSHTML* -файлов), минимально требует нацеливание `netcoreapp3.0` или более поздней версии и имеет `FrameworkReference` в файле проекта [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Использование пакета SDK для Razor для создания библиотеки классов с представлениями Razor или страницами Razor:

* Используйте `Microsoft.NET.Sdk.Razor` вместо `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Как правило, ссылки на пакет `Microsoft.AspNetCore.Mvc` требуется для получения дополнительные зависимости, необходимые для создания и компиляции Razor Pages и представлениями Razor. Как минимум проект следует добавить ссылки на пакеты для:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design` Пакет содержит задачи компиляции Razor и целевых объектов для проекта.

  Предыдущие пакеты включены в `Microsoft.AspNetCore.Mvc`. В следующей разметке показан файл проекта, который использует пакет SDK для Razor для создания файлов Razor для приложения ASP.NET Core Razor Pages:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` И `Microsoft.AspNetCore.Mvc.Razor.Extensions` пакеты входят в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Тем не менее тем меньше версии `Microsoft.AspNetCore.App` ссылки на пакет предоставляет метапакет к приложению, которое не включает последнюю версию `Microsoft.AspNetCore.Razor.Design`. Проекты должны ссылаться на согласованность версий `Microsoft.AspNetCore.Razor.Design` (или `Microsoft.AspNetCore.Mvc`), чтобы включаются последние исправления во время сборки для Razor. Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Свойства

Следующие свойства управляют поведением пакета SDK для Razor в ходе создания проекта:

* `RazorCompileOnBuild` &ndash; Когда `true`, компилирует и сборка Razor как часть сборки проекта. По умолчанию — `true`.
* `RazorCompileOnPublish` &ndash; Когда `true`, компиляция и сборка Razor как часть публикации проекта. По умолчанию — `true`.

Свойства и элементы в следующей таблице используются для настройки входных и выходных данных в пакет SDK для Razor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Начиная с ASP.NET Core 3,0, представления MVC или Razor Pages не обслуживаются по умолчанию, если в файле проекта отключены свойства `RazorCompileOnBuild` или `RazorCompileOnPublish` MSBuild. Приложения должны добавить явную ссылку на пакет [Microsoft. AspNetCore. MVC. Razor. рунтимекомпилатион](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) , если приложение использует компиляцию с помощью среды выполнения для обработки *CSHTML* файлов.

::: moniker-end

| Элементы | Описание |
| ----- | ----------- |
| `RazorGenerate` | Элементы Item (*CSHTML* -файлы), которые являются входными данными для создания кода. |
| `RazorComponent` | Элементы элементов (*Razor* -файлы), которые являются входными данными для создания кода компонента Razor. |
| `RazorCompile` | Элементы Item (*CS* -файлы), входные в целевые объекты компиляции Razor. Используйте этот `ItemGroup`, чтобы указать дополнительные файлы для компиляции в сборку Razor. |
| `RazorTargetAssemblyAttribute` | Элементы, используемые для создания атрибутов для сборки Razor. Например:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Элемент элементы добавляются в виде внедренных ресурсов на созданную сборку Razor. |

| Идентификаторы | Описание |
| -------- | ----------- |
| `RazorTargetName` | Имя файла (без расширения) для сборки, созданной Razor. |
| `RazorOutputPath` | Выходной каталог Razor. |
| `RazorCompileToolset` | Используется для определения набора инструментов для построения сборки Razor. Допустимые значения: `Implicit`, `RazorSDK` и `PrecompilationTool`. |
| [енабледефаултконтентитемс](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Значение по умолчанию — `true`. При `true`включает файлы *Web. config*, *. JSON*и *. cshtml* в качестве содержимого в проекте. Когда я ссылаюсь через `Microsoft.NET.Sdk.Web`, файлы в разделе *wwwroot* и файлы конфигурации, также будут включены. |
| `EnableDefaultRazorGenerateItems` | При значении `true` включает файлы *.cshtml* из элементов `Content` в элементы `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Когда `true`, приводит к возникновению ошибки *.cs* файл, содержащий атрибуты, указанные фильтром `RazorAssemblyAttribute` и файл в выходных данных компиляции. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | При значении `true` добавляет стандартный набор атрибутов сборки в `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Когда `true`, копии `RazorGenerate` элементы ( *.cshtml*) файлы в каталог публикации. Как правило файлы Razor не требуются для опубликованного приложения, если они участвуют в компиляции во время сборки или во время публикации. По умолчанию — `false`. |
| `CopyRefAssembliesToPublishDirectory` | При значении `true` копирует элементы базовой сборки в каталог публикации. Как правило ссылочные сборки не являются обязательными для опубликованного приложения случае во время сборки или публикации во время компиляции Razor. Значение `true` Если опубликованного приложения требует компиляции во время выполнения. Например, значение равно `true` Если приложение изменяет *.cshtml* файлы во время выполнения или использует внедренные представления. По умолчанию — `false`. |
| `IncludeRazorContentInPack` | Когда `true`, все элементы содержимого Razor ( *.cshtml* файлы) отмечены для включения в создаваемый пакет NuGet. По умолчанию — `false`. |
| `EmbedRazorGenerateSources` | При значении `true` добавляет элементы RazorGenerate ( *.cshtml*) в виде внедренных файлов к создаваемой сборке Razor. По умолчанию — `false`. |
| `UseRazorBuildServer` | При значении `true` использует серверный процесс постоянной сборки для разгрузки работы по созданию кода. По умолчанию используется значение `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | При `true`пакет SDK создает дополнительные атрибуты, используемые MVC во время выполнения для выполнения обнаружения частей приложения. |
| `DefaultWebContentItemExcludes` | Шаблон глобализации для элементов элемента, которые должны быть исключены из группы элементов `Content` в проектах, предназначенных для веб-пакета или SDK Razor |
| `ExcludeConfigFilesFromBuildOutput` | Если `true`, файлы *config* и *JSON* не копируются в выходной каталог сборки. |
| `AddRazorSupportForMvc` | При `true`настраивает пакет SDK для Razor для добавления поддержки конфигурации MVC, которая необходима при создании приложений, содержащих представления MVC или Razor Pages. Это свойство неявно задано для проектов .NET Core 3,0 или более поздней версии, предназначенных для веб-пакета SDK |
| `RazorLangVersion` | Целевая версия языка Razor. |

Дополнительные сведения о свойствах см. в статье [MSBuild Properties](/visualstudio/msbuild/msbuild-properties) (Свойства MSBuild).

### <a name="targets"></a>Целевые объекты

Пакет SDK для Razor определяет два основных целевых объекта:

* `RazorGenerate` &ndash; Код создает *.cs* файлов из `RazorGenerate` элементы item. Используйте свойство `RazorGenerateDependsOn`, чтобы указать дополнительные целевые объекты, которые могут выполняться до или после этого целевого объекта.
* `RazorCompile` &ndash; Компилирует созданный *.cs* файлы в сборку Razor. Используйте `RazorCompileDependsOn`, чтобы указать дополнительные целевые объекты, которые могут выполняться до или после этого целевого объекта.
* `RazorComponentGenerate` &ndash; код создает файлы *. CS* для элементов `RazorComponent` элементов. Используйте свойство `RazorComponentGenerateDependsOn`, чтобы указать дополнительные целевые объекты, которые могут выполняться до или после этого целевого объекта.

### <a name="runtime-compilation-of-razor-views"></a>Компиляция среды выполнения представлений Razor

* По умолчанию пакет SDK для Razor не публикует базовые сборки, необходимые для компиляции среды выполнения. Это приведет к сбою компиляции, если модель приложения зависит от компиляции среды выполнения &mdash; например, приложение использует внедренные представления или меняет представления после публикации. Установите для `CopyRefAssembliesToPublishDirectory` значение `true`, чтобы продолжить публикацию базовых сборок.

* Для веб-приложения, убедитесь в приложение предназначено для `Microsoft.NET.Sdk.Web` пакета SDK.

## <a name="razor-language-version"></a>Версия языка Razor

При использовании пакета SDK для `Microsoft.NET.Sdk.Web` версия языка Razor выводится из целевой версии платформы приложения. Для проектов, предназначенных для `Microsoft.NET.Sdk.Razor` SDK, или в редких случаях, когда приложению требуется другая версия языка Razor, чем выводимое значение, можно настроить версию, задав свойство `<RazorLangVersion>` в файле проекта приложения:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Языковая версия Razor тесно интегрирована с версией среды выполнения, для которой она была создана. Нацеливание на языковую версию, не предназначенную для среды выполнения, не поддерживается и, скорее всего, приведет к ошибкам сборки.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Дополнения к формату CSPROJ для .NET Core](/dotnet/core/tools/csproj)
* [Общие элементы проектов MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
