---
title: ASP.NET Core Blazor コンポーネントの仮想化
author: guardrex
description: ASP.NET Core Blazor アプリでコンポーネントの仮想化を使用する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 920a23aee0d0555e93c829142700709d5881afd2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753096"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor コンポーネントの仮想化

作成者: [Daniel Roth](https://github.com/danroth27)

Blazor フレームワークに組み込まれている仮想化サポートを使用して、コンポーネント レンダリングの認識されるパフォーマンスを向上させます。 仮想化は、UI レンダリングを現在表示されている部分のみに制限するための手法です。 たとえば、仮想化が有用なのは、アプリで項目の長いリストや項目の一部のみをレンダリングする必要があるが、表示する必要があるのはどんなときでも項目のサブセットのみである場合です。 Blazor には、仮想化をアプリのコンポーネントに追加するために使用できる `Virtualize` コンポーネントが用意されています。

仮想化を使用しない場合は、一般的なリストで、C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループを使用してリストの各項目を表示できます。

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

リストに何千もの項目が含まれている場合は、リストの表示に時間がかかることがあります。 UI の表示が明らかに遅いとユーザーが感じる場合があります。

リスト内の各項目を一度に表示するのではなく、[`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) ループを `Virtualize` コンポーネントに置き換え、`Items` を使用して固定の項目ソースを指定します。 現在表示される項目のみがレンダリングされます。

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

`Context` を使用してコンポーネントにコンテキストを指定しない場合は、項目コンテンツ テンプレートで `context` 値 (`@context.{PROPERTY}`) を使用します。

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

`Virtualize` コンポーネントにより、コンテナーの高さとレンダリングする項目のサイズに基づいて、レンダリングする項目の数が計算されます。

`Virtualize` コンポーネントの項目コンテンツには次を含めることができます。

* 前の例に含まれていたプレーン HTML および Razor コード。
* 1 つまたは複数の Razor コンポーネント。
* HTML/Razor および Razor コンポーネントの混合。

## <a name="item-provider-delegate"></a>項目プロバイダー デリゲート

すべての項目をメモリに読み込みたくない場合は、要求された項目をオンデマンドで非同期的に取得するコンポーネントの `ItemsProvider` パラメーターに項目プロバイダー デリゲート メソッドを指定できます。

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

項目プロバイダーは、特定の開始インデックスを開始位置として、必要な項目数を指定する `ItemsProviderRequest` を受け取ります。 次に、項目プロバイダーにより、要求される項目がデータベースまたは他のサービスから取得され、それらが `ItemsProviderResult<TItem>` として、合計項目数と共に返されます。 項目プロバイダーでは、項目を要求ごとに取得するか、すぐに使用できるようにキャッシュするかを選択できます。

`Virtualize` コンポーネントでは、そのパラメーターから **1 つの項目ソース** のみを受け入れることができるので、項目プロバイダーを同時に使用して `Items` にコレクションを割り当てることは避けてください。 両方が割り当てられている場合は、コンポーネントのパラメーターが実行時に設定されると <xref:System.InvalidOperationException> がスローされます。

次の例では、従業員が `EmployeeService` から読み込まれます。

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

## <a name="placeholder"></a>プレースホルダー

リモート データ ソースからの項目の要求には時間がかかる場合があるため、項目データが使用可能になるまでプレースホルダー (`<Placeholder>...</Placeholder>`) をレンダリングするオプションがあります。

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

## <a name="item-size"></a>アイテムのサイズ

各項目のサイズは `ItemSize` で設定できます (既定値:50px):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>オーバースキャン数

`OverscanCount` を使用して、表示領域の前後にレンダリングされる追加項目の数を指定します。 この設定は、スクロール中のレンダリングの頻度を減らすのに利用できます。 ただし、値を大きくすると、ページにレンダリングされる要素が多くなります (既定値:3)。

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>状態変更

`Virtualize` コンポーネントによってレンダリングされる項目を変更する場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出して、強制的にコンポーネントを再評価して再レンダリングします。
