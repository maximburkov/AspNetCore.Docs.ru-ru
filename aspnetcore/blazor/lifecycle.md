---
title: Жизненный цикл Blazor ASP.NET Core
author: guardrex
description: Узнайте, как использовать методы жизненного цикла компонента Razor в ASP.NET Core Blazor приложениях.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: e600e7c7a6a8c646a655520bd5c127f2cd662753
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944035"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="aca26-103">Жизненный цикл Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aca26-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="aca26-104">[Люк ЛаСаМ](https://github.com/guardrex) и [Даниэль Roth)](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="aca26-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="aca26-105">Платформа Blazor содержит синхронные и асинхронные методы жизненного цикла.</span><span class="sxs-lookup"><span data-stu-id="aca26-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="aca26-106">Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="aca26-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="aca26-107">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="aca26-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="aca26-108">Методы инициализации компонентов</span><span class="sxs-lookup"><span data-stu-id="aca26-108">Component initialization methods</span></span>

<span data-ttu-id="aca26-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> выполнить код, который инициализирует компонент.</span><span class="sxs-lookup"><span data-stu-id="aca26-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> execute code that initializes a component.</span></span> <span data-ttu-id="aca26-110">Эти методы вызываются только один раз при первом создании экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="aca26-110">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="aca26-111">Для выполнения асинхронной операции используйте `OnInitializedAsync` и ключевое слово `await` в операции:</span><span class="sxs-lookup"><span data-stu-id="aca26-111">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="aca26-112">Асинхронная работа во время инициализации компонента должна происходить во время события жизненного цикла `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="aca26-112">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="aca26-113">Для синхронной операции используйте `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="aca26-113">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="aca26-114">Перед установкой параметров</span><span class="sxs-lookup"><span data-stu-id="aca26-114">Before parameters are set</span></span>

<span data-ttu-id="aca26-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> задает параметры, предоставляемые родительским компонентом компонента в дереве визуализации:</span><span class="sxs-lookup"><span data-stu-id="aca26-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="aca26-116"><xref:Microsoft.AspNetCore.Components.ParameterView> содержит весь набор значений параметров при каждом вызове `SetParametersAsync`.</span><span class="sxs-lookup"><span data-stu-id="aca26-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="aca26-117">Реализация `SetParametersAsync` по умолчанию задает значение каждого свойства с атрибутом `[Parameter]` или `[CascadingParameter]`, имеющим соответствующее значение в `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="aca26-117">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="aca26-118">Параметры, у которых нет соответствующего значения в `ParameterView`, остаются неизменными.</span><span class="sxs-lookup"><span data-stu-id="aca26-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="aca26-119">Если `base.SetParametersAync` не вызывается, Пользовательский код может интерпретировать значение входящих параметров любым необходимым образом.</span><span class="sxs-lookup"><span data-stu-id="aca26-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="aca26-120">Например, нет необходимости назначать входящие параметры свойствам класса.</span><span class="sxs-lookup"><span data-stu-id="aca26-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="aca26-121">После установки параметров</span><span class="sxs-lookup"><span data-stu-id="aca26-121">After parameters are set</span></span>

<span data-ttu-id="aca26-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> вызываются, когда компонент получил параметры от родительского элемента, и значения присваиваются свойствам.</span><span class="sxs-lookup"><span data-stu-id="aca26-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="aca26-123">Эти методы выполняются после инициализации компонента и каждый раз, когда задаются новые значения параметров:</span><span class="sxs-lookup"><span data-stu-id="aca26-123">These methods are executed after component initialization and each time new parameter values are specified:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="aca26-124">Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла `OnParametersSetAsync`.</span><span class="sxs-lookup"><span data-stu-id="aca26-124">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="aca26-125">После подготовки к просмотру компонента</span><span class="sxs-lookup"><span data-stu-id="aca26-125">After component render</span></span>

<span data-ttu-id="aca26-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> вызываются после завершения подготовки компонента к просмотру.</span><span class="sxs-lookup"><span data-stu-id="aca26-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="aca26-127">В этот момент заполнены ссылки на элементы и компоненты.</span><span class="sxs-lookup"><span data-stu-id="aca26-127">Element and component references are populated at this point.</span></span> <span data-ttu-id="aca26-128">Используйте этот этап для выполнения дополнительных шагов инициализации с помощью готового к просмотру содержимого, например для активации сторонних библиотек JavaScript, которые работают с визуализированными элементами DOM.</span><span class="sxs-lookup"><span data-stu-id="aca26-128">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="aca26-129">Параметр `firstRender` для `OnAfterRenderAsync` и `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="aca26-129">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="aca26-130">Устанавливается в значение `true` при первом отображении экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="aca26-130">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="aca26-131">Можно использовать, чтобы гарантировать, что работа по инициализации выполняется только один раз.</span><span class="sxs-lookup"><span data-stu-id="aca26-131">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="aca26-132">Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="aca26-132">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="aca26-133">Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из `OnAfterRenderAsync`, платформа не планирует дальнейший цикл рендеринга компонента после завершения задачи.</span><span class="sxs-lookup"><span data-stu-id="aca26-133">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="aca26-134">Это позволяет избежать бесконечного цикла отрисовки.</span><span class="sxs-lookup"><span data-stu-id="aca26-134">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="aca26-135">Он отличается от других методов жизненного цикла, которые запланируют дальнейший цикл рендеринга после завершения возвращаемой задачи.</span><span class="sxs-lookup"><span data-stu-id="aca26-135">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="aca26-136">`OnAfterRender` и `OnAfterRenderAsync` *не вызываются при предварительной отрисовке на сервере.*</span><span class="sxs-lookup"><span data-stu-id="aca26-136">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="aca26-137">Подавлять обновление пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="aca26-137">Suppress UI refreshing</span></span>

<span data-ttu-id="aca26-138">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>, чтобы отключить обновление пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="aca26-138">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="aca26-139">Если реализация возвращает `true`, Пользовательский интерфейс обновляется:</span><span class="sxs-lookup"><span data-stu-id="aca26-139">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="aca26-140">`ShouldRender` вызывается каждый раз при подготовке к просмотру компонента.</span><span class="sxs-lookup"><span data-stu-id="aca26-140">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="aca26-141">Даже при переопределении `ShouldRender` компонент всегда первоначально готовится к просмотру.</span><span class="sxs-lookup"><span data-stu-id="aca26-141">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="aca26-142">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="aca26-142">State changes</span></span>

<span data-ttu-id="aca26-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> уведомляет компонент о том, что его состояние изменилось.</span><span class="sxs-lookup"><span data-stu-id="aca26-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="aca26-144">Если применимо, вызов `StateHasChanged` вызывает переподготовку компонента.</span><span class="sxs-lookup"><span data-stu-id="aca26-144">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="aca26-145">Обработка незавершенных асинхронных действий при отрисовке</span><span class="sxs-lookup"><span data-stu-id="aca26-145">Handle incomplete async actions at render</span></span>

<span data-ttu-id="aca26-146">Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до подготовки компонента к просмотру.</span><span class="sxs-lookup"><span data-stu-id="aca26-146">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="aca26-147">Во время выполнения метода жизненного цикла объекты могут быть `null`ы или заполнены незаполненными данными.</span><span class="sxs-lookup"><span data-stu-id="aca26-147">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="aca26-148">Предоставьте логику отрисовки для подтверждения инициализации объектов.</span><span class="sxs-lookup"><span data-stu-id="aca26-148">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="aca26-149">Отрисовывает элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты `null`.</span><span class="sxs-lookup"><span data-stu-id="aca26-149">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="aca26-150">В компоненте `FetchData` шаблонов Blazor `OnInitializedAsync` переопределяется в асичронаусли получения данных прогноза (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="aca26-150">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="aca26-151">Если `forecasts` `null`, пользователю выводится сообщение о загрузке.</span><span class="sxs-lookup"><span data-stu-id="aca26-151">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="aca26-152">После завершения `Task`, возвращенного `OnInitializedAsync`, компонент перерисовывается с обновленным состоянием.</span><span class="sxs-lookup"><span data-stu-id="aca26-152">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="aca26-153">*Pages/FetchData. Razor* в шаблоне сервера Blazor:</span><span class="sxs-lookup"><span data-stu-id="aca26-153">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="aca26-154">Освобождение компонентов с помощью IDisposable</span><span class="sxs-lookup"><span data-stu-id="aca26-154">Component disposal with IDisposable</span></span>

<span data-ttu-id="aca26-155">Если компонент реализует <xref:System.IDisposable>, [метод Dispose](/dotnet/standard/garbage-collection/implementing-dispose) вызывается при удалении компонента из пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="aca26-155">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="aca26-156">Следующий компонент использует `@implements IDisposable` и метод `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="aca26-156">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="aca26-157">Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> в `Dispose` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="aca26-157">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="aca26-158">`StateHasChanged` могут быть вызваны при разрыве модуля подготовки отчетов, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="aca26-158">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="aca26-159">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="aca26-159">Handle errors</span></span>

<span data-ttu-id="aca26-160">Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="aca26-160">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>