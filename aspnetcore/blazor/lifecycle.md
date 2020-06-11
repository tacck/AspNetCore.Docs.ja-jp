---
title: "title:'ASP.NET Core Blazor ライフサイクル' author: description:'ASP.NET Core Blazor アプリで Razor コンポーネント ライフサイクル メソッドを使用する方法について学習します。'"
author: guardrex
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 9dcbb2ca21cc689063198e1ccc90583db4229183
ms.sourcegitcommit: ea98ab8b2f61aa09f2d74edbb62db339fa06f105
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83864588"
---
# <a name="aspnet-core-blazor-lifecycle"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor'

## <a name="lifecycle-methods"></a>'SignalR' uid:

### <a name="component-initialization-methods"></a>ASP.NET Core Blazor ライフサイクル

著者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27) Blazor フレームワークには、同期と非同期のライフサイクル メソッドが含まれています。

コンポーネントの初期化およびレンダリング中にコンポーネントで追加の操作を実行するには、ライフサイクル メソッドをオーバーライドします。

```csharp
protected override void OnInitialized()
{
    ...
}
```

ライフサイクル メソッド

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

コンポーネントの初期化メソッド

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> は、コンポーネントが、その親コンポーネントから初期パラメーターを受け取った後で初期化されるときに呼び出されます。
* コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> を使用します。

同期操作の場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> をオーバーライドします。

非同期操作を実行するには、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> をオーバーライドし、操作で [await](/dotnet/csharp/language-reference/operators/await) 演算子を使用します。 [コンテンツをプリレンダリングする ](xref:blazor/hosting-model-configuration#render-mode)Blazor サーバー アプリは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> を "**_2 回_**" 呼び出します。 コンポーネントが最初にページの一部として静的にレンダリングされるときに 1 回。

ブラウザーがサーバーへの接続を確立するときに 2 回目。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 内で開発者コードが 2 回実行されないようにするには、「[プリレンダリング後のステートフル再接続](#stateful-reconnection-after-prerendering)」セクションを参照してください。

### <a name="before-parameters-are-set"></a>Blazor サーバー アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。

コンポーネントは、プリレンダリング時に異なるレンダリングが必要になる場合があります。

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

詳細については、「[アプリがプリレンダリングされていることを検出する](#detect-when-the-app-is-prerendering)」セクションを参照してください。

イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。 詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。

パラメーターが設定される前 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> は、レンダリング ツリーのコンポーネントの親によって指定されたパラメーターを設定します。

<xref:Microsoft.AspNetCore.Components.ParameterView> には、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> が呼び出されるたびに、パラメーター値のセット全体が含まれます。 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> の既定の実装では、対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にある [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) または [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 属性を使用して、各プロパティの値が設定されます。

### <a name="after-parameters-are-set"></a>対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にないパラメーターは、変更されないままになります。

[base.SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) が呼び出されない場合、カスタム コードでは、必要に応じて受信パラメーター値を解釈できます。

* たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。
* イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。
  * 詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。
  * パラメーターが設定された後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> が呼び出されます。

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> コンポーネントが初期化され、その親コンポーネントからパラメーターの最初のセットを受け取ったとき。

```csharp
protected override void OnParametersSet()
{
    ...
}
```

親コンポーネントが再レンダリングし、次のものを提供するとき: 少なくとも 1 つのパラメーターが変更された既知のプリミティブ不変型のみ。

### <a name="after-component-render"></a>任意の複合型のパラメーター。

フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーター セットは変更済みとして扱われます。 パラメーターとプロパティ値を適用するときの非同期処理は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ライフサイクル イベント中に発生する必要があります。 イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。

詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。

* コンポーネントのレンダリング後
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> は、コンポーネントのレンダリングが完了した後に呼び出されます。

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
> この時点で、要素およびコンポーネント参照が設定されます。
>
> レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を行うには、この段階を使用します。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> の `firstRender` パラメーター: コンポーネント インスタンスを初めて表示するときに `true` に設定されます。

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

初期化作業が確実に 1 回だけ実行されるように使用できます。

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ライフサイクル イベント中に、レンダリング直後の非同期作業が発生する必要があります。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークでは、そのタスクが完了しても、コンポーネントに対してさらにレンダリング サイクルがスケジュールされることはありません。

### <a name="suppress-ui-refreshing"></a>これは、無限のレンダリング ループを回避するためです。

返されたタスクが完了すると、さらにレンダリング サイクルをスケジュールする他のライフサイクル メソッドとは異なります。 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> は、"*サーバー上でプリレンダリングするときには呼び出されません。* "

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。

詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。

UI 更新の抑制

## <a name="state-changes"></a><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> をオーバーライドして、UI の更新を抑制します。

実装によって `true` が返された場合は、UI が更新されます。 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> は、コンポーネントがレンダリングされるたびに呼び出されます。

## <a name="handle-incomplete-async-actions-at-render"></a><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> がオーバーライドされる場合でも、コンポーネントは常に最初にレンダリングされます。

詳細については、「<xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>」を参照してください。 状態変更 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、状態が変更されたことをコンポーネントに通知します。 必要に応じて、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すと、コンポーネントが再レンダリングされます。

レンダリング時の不完全な非同期アクションを処理する ライフサイクル イベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。 ライフサイクル メソッドの実行中に、オブジェクトが `null` またはデータが不完全に設定されている可能性があります。

オブジェクトが初期化されていることを確認するレンダリング ロジックを提供します。

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>オブジェクトが `null` の間、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。

Blazor テンプレートの `FetchData` コンポーネントでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> はオーバーライドされ、予測データを非同期に受信します (`forecasts`)。 `forecasts` が `null` の場合、読み込みメッセージがユーザーに表示されます。

```razor
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
> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> によって返された `Task` が完了すると、コンポーネントは更新された状態で再レンダリングされます。 Blazor サーバー テンプレート内の *Pages/FetchData.razor*:

IDisposable を使用したコンポーネントの破棄 コンポーネントが <xref:System.IDisposable> を実装している場合は、コンポーネントが UI から削除されると、[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。

* 次のコンポーネントでは、`@implements IDisposable` および `Dispose` メソッドが使用されます。

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* `Dispose` では、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> の呼び出しはサポートされていません。

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。

.NET イベントからイベント ハンドラーのサブスクライブを解除します。

## <a name="stateful-reconnection-after-prerendering"></a>次の [Blazor フォーム](xref:blazor/forms-validation)の例は、`Dispose` メソッドでイベント ハンドラーをアンフックする方法を示しています。

プライベート フィールドとラムダのアプローチ プライベート メソッドのアプローチ エラーの処理

* ライフサイクル メソッド実行中のエラー処理の詳細については、「<xref:blazor/handle-errors#lifecycle-methods>」を参照してください。
* プリレンダリング後のステートフル再接続

Blazor サーバー アプリで <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> が <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> の場合、コンポーネントは最初にページの一部として静的にレンダリングされます。

ブラウザーがサーバーへの接続を確立すると、コンポーネントが "*再度*" レンダリングされ、コンポーネントがやりとりできるようになります。

* コンポーネントを初期化するための [OnInitialized{Async}](#component-initialization-methods) ライフサイクル メソッドが存在する場合、メソッドは "*2 回*" 実行されます。
* コンポーネントが静的にプリレンダリングされたとき。
* サーバー接続が確立された後。

これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変わる可能性があります。

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

Blazor サーバー アプリ内の二重レンダリングのシナリオを回避するには、次の手順を行います。

## <a name="detect-when-the-app-is-prerendering"></a>プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a>識別子をプリレンダリング中に使用して、コンポーネントの状態を保存します。

識別子をプリレンダリング後に使用して、キャッシュされた状態を取得します。 次のコードは、二重レンダリングを回避するテンプレートベースの Blazor サーバー アプリ内で更新される `WeatherForecastService` を示しています。 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> の詳細については、「<xref:blazor/hosting-model-configuration#render-mode>」を参照してください。

アプリがプリレンダリングされていることを検出する

* 取り消し可能なバックグラウンド作業
* ネットワーク呼び出し (<xref:System.Net.Http.HttpClient>) の実行やデータベースとの対話など、コンポーネントによって実行時間の長いバックグラウンド作業が実行されることがよくあります。
* いくつかの状況でシステム リソースを節約するために、バックグラウンド作業を停止することをお勧めします。
* たとえば、ユーザーがコンポーネントの操作を止めても、バックグラウンドの非同期操作は自動的に停止しません。
* バックグラウンド作業項目の取り消しが必要になるその他の理由には、次のようなものがあります。

実行中のバックグラウンド タスクは、不完全な入力データまたは処理パラメーターを使用して開始されました。

* 現在実行中のバックグラウンド作業項目のセットを、新しい作業項目のセットに置き換える必要があります。
* 現在実行中のタスクの優先度を変更する必要があります。
* アプリをサーバーに再展開するには、シャットダウンする必要があります。

サーバー リソースが制限され、バックグラウンド作業項目の再スケジュールが必要になりました。

* コンポーネントに取り消し可能なバックグラウンド作業パターンを実装するには:
* <xref:System.Threading.CancellationTokenSource> と <xref:System.Threading.CancellationToken> を使用します。

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
