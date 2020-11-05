---
title: 'ASP.NET Core :::no-loc(Blazor)::: コンポーネントの仮想化'
author: guardrex
description: 'ASP.NET Core :::no-loc(Blazor)::: アプリでコンポーネントの仮想化を使用する方法について説明します。'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/virtualization
ms.openlocfilehash: b23e4814daaabbe2c8660d49cc5b6940a9cc3b4f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056167"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="967c5-103">ASP.NET Core :::no-loc(Blazor)::: コンポーネントの仮想化</span><span class="sxs-lookup"><span data-stu-id="967c5-103">ASP.NET Core :::no-loc(Blazor)::: component virtualization</span></span>

<span data-ttu-id="967c5-104">作成者: [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="967c5-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="967c5-105">:::no-loc(Blazor)::: フレームワークに組み込まれている仮想化サポートを使用して、コンポーネント レンダリングの認識されるパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="967c5-105">Improve the perceived performance of component rendering using the :::no-loc(Blazor)::: framework's built-in virtualization support.</span></span> <span data-ttu-id="967c5-106">仮想化は、UI レンダリングを現在表示されている部分のみに制限するための手法です。</span><span class="sxs-lookup"><span data-stu-id="967c5-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="967c5-107">たとえば、仮想化が有用なのは、アプリで項目の長いリストや項目の一部のみをレンダリングする必要があるが、表示する必要があるのはどんなときでも項目のサブセットのみである場合です。</span><span class="sxs-lookup"><span data-stu-id="967c5-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="967c5-108">:::no-loc(Blazor)::: には、仮想化をアプリのコンポーネントに追加するために使用できる `Virtualize` コンポーネントが用意されています。</span><span class="sxs-lookup"><span data-stu-id="967c5-108">:::no-loc(Blazor)::: provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="967c5-109">仮想化を使用しない場合は、一般的なリストで、C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループを使用してリストの各項目を表示できます。</span><span class="sxs-lookup"><span data-stu-id="967c5-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="967c5-110">リストに何千もの項目が含まれている場合は、リストの表示に時間がかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="967c5-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="967c5-111">UI の表示が明らかに遅いとユーザーが感じる場合があります。</span><span class="sxs-lookup"><span data-stu-id="967c5-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="967c5-112">リスト内の各項目を一度に表示するのではなく、[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループを `Virtualize` コンポーネントに置き換え、`Items` を使用して固定の項目ソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="967c5-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="967c5-113">現在表示される項目のみがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="967c5-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="967c5-114">`Context` を使用してコンポーネントにコンテキストを指定しない場合は、項目コンテンツ テンプレートで `context` 値 (`@context.{PROPERTY}`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="967c5-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="967c5-115">`Virtualize` コンポーネントにより、コンテナーの高さとレンダリングする項目のサイズに基づいて、レンダリングする項目の数が計算されます。</span><span class="sxs-lookup"><span data-stu-id="967c5-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="967c5-116">`Virtualize` コンポーネントの項目コンテンツには次を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="967c5-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="967c5-117">前の例に含まれていたプレーン HTML および :::no-loc(Razor)::: コード。</span><span class="sxs-lookup"><span data-stu-id="967c5-117">Plain HTML and :::no-loc(Razor)::: code, as the preceding example shows.</span></span>
* <span data-ttu-id="967c5-118">1 つまたは複数の :::no-loc(Razor)::: コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="967c5-118">One or more :::no-loc(Razor)::: components.</span></span>
* <span data-ttu-id="967c5-119">HTML/:::no-loc(Razor)::: および :::no-loc(Razor)::: コンポーネントの混合。</span><span class="sxs-lookup"><span data-stu-id="967c5-119">A mix of HTML/:::no-loc(Razor)::: and :::no-loc(Razor)::: components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="967c5-120">項目プロバイダー デリゲート</span><span class="sxs-lookup"><span data-stu-id="967c5-120">Item provider delegate</span></span>

<span data-ttu-id="967c5-121">すべての項目をメモリに読み込みたくない場合は、要求された項目をオンデマンドで非同期的に取得するコンポーネントの `ItemsProvider` パラメーターに項目プロバイダー デリゲート メソッドを指定できます。</span><span class="sxs-lookup"><span data-stu-id="967c5-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="967c5-122">項目プロバイダーは、特定の開始インデックスを開始位置として、必要な項目数を指定する `ItemsProviderRequest` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="967c5-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="967c5-123">次に、項目プロバイダーにより、要求される項目がデータベースまたは他のサービスから取得され、それらが `ItemsProviderResult<TItem>` として、合計項目数と共に返されます。</span><span class="sxs-lookup"><span data-stu-id="967c5-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="967c5-124">項目プロバイダーでは、項目を要求ごとに取得するか、すぐに使用できるようにキャッシュするかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="967c5-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="967c5-125">項目プロバイダーを使用して、同じ `Virtualize` コンポーネントの `Items` にコレクションを割り当てないでください。</span><span class="sxs-lookup"><span data-stu-id="967c5-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="967c5-126">次の例では、従業員が `EmployeeService` から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="967c5-126">The following example loads employees from an `EmployeeService`:</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a><span data-ttu-id="967c5-127">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="967c5-127">Placeholder</span></span>

<span data-ttu-id="967c5-128">リモート データ ソースからの項目の要求には時間がかかる場合があるため、項目データが使用可能になるまでプレースホルダー (`<Placeholder>...</Placeholder>`) をレンダリングするオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="967c5-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="967c5-129">アイテムのサイズ</span><span class="sxs-lookup"><span data-stu-id="967c5-129">Item size</span></span>

<span data-ttu-id="967c5-130">各項目のサイズは `ItemSize` で設定できます (既定値:50px):</span><span class="sxs-lookup"><span data-stu-id="967c5-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="967c5-131">オーバースキャン数</span><span class="sxs-lookup"><span data-stu-id="967c5-131">Overscan count</span></span>

<span data-ttu-id="967c5-132">`OverscanCount` を使用して、表示領域の前後にレンダリングされる追加項目の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="967c5-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="967c5-133">この設定は、スクロール中のレンダリングの頻度を減らすのに利用できます。</span><span class="sxs-lookup"><span data-stu-id="967c5-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="967c5-134">ただし、値を大きくすると、ページにレンダリングされる要素が多くなります (既定値:3)。</span><span class="sxs-lookup"><span data-stu-id="967c5-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="967c5-135">状態変更</span><span class="sxs-lookup"><span data-stu-id="967c5-135">State changes</span></span>

<span data-ttu-id="967c5-136">`Virtualize` コンポーネントによってレンダリングされる項目を変更する場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出して、強制的にコンポーネントを再評価して再レンダリングします。</span><span class="sxs-lookup"><span data-stu-id="967c5-136">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
