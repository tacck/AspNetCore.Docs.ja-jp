---
title: ASP.NET Core Blazor コンポーネントの仮想化
author: guardrex
description: ASP.NET Core Blazor アプリでコンポーネントの仮想化を使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/virtualization
ms.openlocfilehash: 9c3e53bee7535b36bba3474ff50a881568bbd690
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393809"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="015d8-103">ASP.NET Core Blazor コンポーネントの仮想化</span><span class="sxs-lookup"><span data-stu-id="015d8-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="015d8-104">作成者: [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="015d8-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="015d8-105">Blazor フレームワークに組み込まれている仮想化サポートを使用して、コンポーネント レンダリングの認識されるパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="015d8-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="015d8-106">仮想化は、UI レンダリングを現在表示されている部分のみに制限するための手法です。</span><span class="sxs-lookup"><span data-stu-id="015d8-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="015d8-107">たとえば、仮想化が有用なのは、アプリで項目の長いリストや項目の一部のみをレンダリングする必要があるが、表示する必要があるのはどんなときでも項目のサブセットのみである場合です。</span><span class="sxs-lookup"><span data-stu-id="015d8-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="015d8-108">Blazor には、仮想化をアプリのコンポーネントに追加するために使用できる `Virtualize` コンポーネントが用意されています。</span><span class="sxs-lookup"><span data-stu-id="015d8-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="015d8-109">仮想化を使用しない場合は、一般的なリストで、C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループを使用してリストの各項目を表示できます。</span><span class="sxs-lookup"><span data-stu-id="015d8-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="015d8-110">リストに何千もの項目が含まれている場合は、リストの表示に時間がかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="015d8-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="015d8-111">UI の表示が明らかに遅いとユーザーが感じる場合があります。</span><span class="sxs-lookup"><span data-stu-id="015d8-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="015d8-112">リスト内の各項目を一度に表示するのではなく、[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループを `Virtualize` コンポーネントに置き換え、`Items` を使用して固定の項目ソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="015d8-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="015d8-113">現在表示される項目のみがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="015d8-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="015d8-114">`Context` を使用してコンポーネントにコンテキストを指定しない場合は、項目コンテンツ テンプレートで `context` 値 (`@context.{PROPERTY}`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="015d8-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="015d8-115">`Virtualize` コンポーネントにより、コンテナーの高さとレンダリングする項目のサイズに基づいて、レンダリングする項目の数が計算されます。</span><span class="sxs-lookup"><span data-stu-id="015d8-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="015d8-116">`Virtualize` コンポーネントの項目コンテンツには次を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="015d8-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="015d8-117">前の例に含まれていたプレーン HTML および Razor コード。</span><span class="sxs-lookup"><span data-stu-id="015d8-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="015d8-118">1 つまたは複数の Razor コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="015d8-118">One or more Razor components.</span></span>
* <span data-ttu-id="015d8-119">HTML/Razor および Razor コンポーネントの混合。</span><span class="sxs-lookup"><span data-stu-id="015d8-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="015d8-120">項目プロバイダー デリゲート</span><span class="sxs-lookup"><span data-stu-id="015d8-120">Item provider delegate</span></span>

<span data-ttu-id="015d8-121">すべての項目をメモリに読み込みたくない場合は、要求された項目をオンデマンドで非同期的に取得するコンポーネントの `ItemsProvider` パラメーターに項目プロバイダー デリゲート メソッドを指定できます。</span><span class="sxs-lookup"><span data-stu-id="015d8-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="015d8-122">項目プロバイダーは、特定の開始インデックスを開始位置として、必要な項目数を指定する `ItemsProviderRequest` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="015d8-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="015d8-123">次に、項目プロバイダーにより、要求される項目がデータベースまたは他のサービスから取得され、それらが `ItemsProviderResult<TItem>` として、合計項目数と共に返されます。</span><span class="sxs-lookup"><span data-stu-id="015d8-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="015d8-124">項目プロバイダーでは、項目を要求ごとに取得するか、すぐに使用できるようにキャッシュするかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="015d8-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="015d8-125">項目プロバイダーを使用して、同じ `Virtualize` コンポーネントの `Items` にコレクションを割り当てないでください。</span><span class="sxs-lookup"><span data-stu-id="015d8-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="015d8-126">次の例では、従業員が `EmployeeService` から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="015d8-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="015d8-127">プレースホルダー</span><span class="sxs-lookup"><span data-stu-id="015d8-127">Placeholder</span></span>

<span data-ttu-id="015d8-128">リモート データ ソースからの項目の要求には時間がかかる場合があるため、項目データが使用可能になるまでプレースホルダー (`<Placeholder>...</Placeholder>`) をレンダリングするオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="015d8-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="015d8-129">アイテムのサイズ</span><span class="sxs-lookup"><span data-stu-id="015d8-129">Item size</span></span>

<span data-ttu-id="015d8-130">各項目のサイズは `ItemSize` で設定できます (既定値:50px):</span><span class="sxs-lookup"><span data-stu-id="015d8-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="015d8-131">オーバースキャン数</span><span class="sxs-lookup"><span data-stu-id="015d8-131">Overscan count</span></span>

<span data-ttu-id="015d8-132">`OverscanCount` を使用して、表示領域の前後にレンダリングされる追加項目の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="015d8-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="015d8-133">この設定は、スクロール中のレンダリングの頻度を減らすのに利用できます。</span><span class="sxs-lookup"><span data-stu-id="015d8-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="015d8-134">ただし、値を大きくすると、ページにレンダリングされる要素が多くなります (既定値:3)。</span><span class="sxs-lookup"><span data-stu-id="015d8-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="015d8-135">たとえば、コンポーネントを含む数百の行をレンダリングするグリッドまたはリストでは、レンダリングにプロセッサが集中的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="015d8-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="015d8-136">コンポーネントのサブセットのみが特定の時点でレンダリングされるように、グリッドまたはリストのレイアウトを仮想化することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="015d8-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="015d8-137">コンポーネントのサブセットのレンダリングの例については、[`Virtualization` サンプル アプリ (aspnet/samples GitHub リポジトリ)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization) にある次のコンポーネントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="015d8-137">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="015d8-138">`Virtualize` コンポーネント ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): ユーザーのスクロールに基づいて一連の気象データ行をレンダリングするために <xref:Microsoft.AspNetCore.Components.ComponentBase> を実装する、C# で記述されたコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="015d8-138">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="015d8-139">`FetchData` コンポーネント ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): 一度に 25 行の気象データを表示するために、`Virtualize` コンポーネントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="015d8-139">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="015d8-140">状態変更</span><span class="sxs-lookup"><span data-stu-id="015d8-140">State changes</span></span>

<span data-ttu-id="015d8-141">`Virtualize` コンポーネントによってレンダリングされる項目を変更する場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出して、強制的にコンポーネントを再評価して再レンダリングします。</span><span class="sxs-lookup"><span data-stu-id="015d8-141">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
