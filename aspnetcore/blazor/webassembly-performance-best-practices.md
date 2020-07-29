---
title: ASP.NET Core [Blazor WebAssembly パフォーマンスに関するベスト プラクティス
author: pranavkm
description: ASP.NET Core [Blazor WebAssembly アプリのパフォーマンスの向上、および一般的なパフォーマンスの問題回避のためのヒント。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: f7bd0d356030e6ddb95c77d7376995320e3ec40e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401884"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>ASP.NET Core [Blazor WebAssembly パフォーマンスに関するベスト プラクティス

作成者: [Pranav Krishnamoorthy](https://github.com/pranavkm)

この記事では、ASP.NET Core [Blazor WebAssembly のパフォーマンスに関するベスト プラクティスのガイドラインを示します。

## <a name="avoid-unnecessary-component-renders"></a>不要なコンポーネントのレンダリングを回避する

[Blazor の差分検出アルゴリズムでは、コンポーネントが変更されていないことがアルゴリズムで認識された場合に、コンポーネントの再レンダリングが回避されます。 コンポーネントのレンダリングを細かく制御するために、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> をオーバーライドします。

初回のレンダリング後に変更されることがない、UI のみのコンポーネントを作成する場合は、`false` を返すように <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を構成します。

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

ほとんどのアプリでは細かい制御は必要ありませんが、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を使用すると、UI イベントに応答するコンポーネントを選択的にレンダリングできます。 多数のコンポーネントがレンダリングされるシナリオでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を使用することが重要な場合もあります。 1 つのグリッドについて考えてみます。グリッドの 1 つのセルにある 1 つのコンポーネントで <xref:Microsoft.AspNetCore.Components.EventCallback> を使用すると、グリッドに対して <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> が呼び出されます。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すと、すべての子コンポーネントが再レンダリングされます。 再レンダリングが必要なセルの数が少ない場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を使用して、不要なレンダリングによってパフォーマンスが低下しないようにします。

次に例を示します。

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> がオーバーライドされ、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> フィールドの値に設定されます。これは、コンポーネントが読み込まれる時点では、最初は `false` です。
* ボタンが選択されると、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> が `true` に設定されます。これにより、コンポーネントが更新された `currentCount` で強制的に再レンダリングされます。
* 再レンダリングの直後、次回ボタンが選択されるまでこれ以上の再レンダリングを回避するため、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> によって <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> の値が `false` に戻されます。

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

詳細については、「<xref:blazor/components/lifecycle#after-component-render>」を参照してください。

## <a name="virtualize-re-usable-fragments"></a>再利用可能なフラグメントを仮想化する

コンポーネントでは、コードとマークアップの再利用可能なフラグメントを生成するための便利なアプローチが提供されます。 一般的には、アプリの要件に最適な個別のコンポーネントを作成することをお勧めします。 注意点の 1 つは、追加の各子コンポーネントが親コンポーネントのレンダリングにかかる合計時間に影響するということです。 ほとんどのアプリでは、追加のオーバーヘッドはごくわずかです。 多数のコンポーネントを生成するアプリでは、レンダリングするコンポーネントの数を制限するなど、処理のオーバーヘッドを減らすための方法の使用を検討する必要があります。

たとえば、コンポーネントを含む数百の行をレンダリングするグリッドまたはリストでは、レンダリングにプロセッサが集中的に使用されます。 コンポーネントのサブセットのみが特定の時点でレンダリングされるように、グリッドまたはリストのレイアウトを仮想化することを検討してください。 コンポーネントのサブセットのレンダリングの例については、[`Virtualization` サンプル アプリ (aspnet/samples GitHub リポジトリ)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization) にある次のコンポーネントを参照してください。

* `Virtualize` コンポーネント ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): ユーザーのスクロールに基づいて一連の気象データ行をレンダリングするために <xref:Microsoft.AspNetCore.Components.ComponentBase> を実装する、C# で記述されたコンポーネント。
* `FetchData` コンポーネント ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): 一度に 25 行の気象データを表示するために、`Virtualize` コンポーネントが使用されます。

## <a name="avoid-javascript-interop-to-marshal-data"></a>データをマーシャリングするための JavaScript 相互運用を回避する

[Blazor WebAssembly では、JavaScript (JS) 相互運用呼び出しは WebAssembly と JS の境界を走査する必要があります。 2 つのコンテキストにわたってコンテンツをシリアル化または逆シリアル化すると、アプリの処理オーバーヘッドが発生します。 多くの場合、JS 相互運用呼び出しを頻繁に行うとパフォーマンスに悪影響を与えることがあります。 境界を越えたデータのマーシャリングを減らすには、アプリによって多数の小さなペイロードを 1 つの大きなペイロードに統合できるかどうかを判断して、WebAssembly と JS の間でコンテキストを頻繁に切り替えることを回避します。

## <a name="use-systemtextjson"></a>System.Text.Json を使用する

[Blazor の JS 相互運用の実装は <xref:System.Text.Json> に依存します。これは、メモリ割り当てが少ない高パフォーマンスの JSON シリアル化ライブラリです。 <xref:System.Text.Json> を使用しても、1 つ以上の代替 JSON ライブラリを追加したときにアプリのペイロードのサイズは増加しません。

移行のガイダンスについては、「[`Newtonsoft.Json` から `System.Text.Json` に移行する方法](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)」を参照してください。

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>必要に応じて、同期およびマーシャリング解除された JS 相互運用 API を使用する

[Blazor WebAssembly では、[Blazor Server アプリで使用できる 1 つのバージョンに対して、<xref:Microsoft.JSInterop.IJSRuntime> の 2 つの追加バージョンが提供されています。

* <xref:Microsoft.JSInterop.IJSInProcessRuntime> を使用して、JS 相互運用呼び出しを同期的に呼び出すことができます。この場合、非同期バージョンよりもオーバーヘッドが少なくなります。

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> によって、マーシャリング解除された JS 相互運用呼び出しが許可されます。

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> を使用すると JS 相互運用アプローチのオーバーヘッドが最小限になりますが、これらの API とのやり取りに必要な JavaScript API は現在ドキュメントに記載されていません。また、今後のリリースでの破壊的変更の対象となることがあります。

## <a name="reduce-app-size"></a>アプリのサイズを小さくする

### <a name="intermediate-language-il-linking"></a>中間言語 (IL) のリンク

[[Blazor WebAssembly アプリをリンク](xref:blazor/host-and-deploy/configure-linker)すると、アプリのバイナリで使用されていないコードをトリミングすることで、アプリのサイズが縮小されます。 既定では、リンカーは `Release` 構成でビルドする場合にのみ有効になります。 これを活用するには、[-c|--configuration](/dotnet/core/tools/dotnet-publish#options) オプションを `Release` に設定した状態で [`dotnet publish`](/dotnet/core/tools/dotnet-publish) コマンドを使用して、展開用にアプリを発行します。

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a>[圧縮]

[Blazor WebAssembly アプリが公開されると、公開中に出力が静的に圧縮されてアプリのサイズが縮小され、実行時の圧縮に必要なオーバーヘッドがなくなります。 [Blazor は、コンテンツ ネゴシエーションの実行および静的に圧縮されたファイルの提供に関して、サーバーに依存します。

アプリが展開されたら、アプリが圧縮ファイルを提供していることを確認します。 ブラウザーの開発者ツールの [ネットワーク] タブを調べ、ファイルが `Content-Encoding: br` または `Content-Encoding: gz` で提供されていることを確認します。 ホストが圧縮ファイルを提供していない場合は、「<xref:blazor/host-and-deploy/webassembly#compression>」に記載されている手順に従ってください。

### <a name="disable-unused-features"></a>未使用の機能を無効にする

[Blazor WebAssembly のランタイムには、次の .NET 機能が含まれています。これらの機能はアプリで必要がない場合、ペイロード サイズを小さくするために無効にすることができます。

* データ ファイルは、タイムゾーン情報を正しく保つために含まれています。 アプリでこの機能を必要としない場合は、アプリのプロジェクト ファイル内の `BlazorEnableTimeZoneSupport` MSBuild プロパティを `false` に設定して無効にすることを検討してください。

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> などの API を正常に動作させるために、照合順序情報が含まれています。 アプリで照合順序情報を必要としないことが確実な場合は、アプリのプロジェクト ファイル内の `BlazorWebAssemblyPreserveCollationData` MSBuild プロパティを `false` に設定して無効にすることを検討してください。

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
