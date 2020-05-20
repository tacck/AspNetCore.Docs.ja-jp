---
title: Blazorパフォーマンスのベストプラクティスを ASP.NET Core
author: pranavkm
description: ASP.NET Core アプリのパフォーマンスを向上させ Blazor 、一般的なパフォーマンスの問題を回避するためのヒントです。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/blazor/webassembly-best-practices
ms.openlocfilehash: 9e9b166cb9ce9870a8ff275b72bb12f04b84751b
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2020
ms.locfileid: "83439423"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>Blazorパフォーマンスのベストプラクティスを ASP.NET Core

[Pranav Krishnamoorthy](https://github.com/pranavkm)

この記事では、パフォーマンスのベストプラクティス ASP.NET Core に関するガイドラインを提供 Blazor します。

## <a name="avoid-unnecessary-component-renders"></a>不要なコンポーネントのレンダリングを回避する

Blazorの比較アルゴリズムは、コンポーネントが変更されていないことをアルゴリズムが認識した場合に、コンポーネントの更新を回避します。 [Componentbase をオーバーライドします。](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A)コンポーネントのレンダリングをきめ細かく制御するためにレンダリングします。

初期レンダリング後に変更されることがない UI のみのコンポーネントを作成する場合は、を次のように構成し `ShouldRender` `false` ます。

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

ほとんどのアプリでは細かい制御は必要ありませんが、 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> UI イベントに応答するコンポーネントを選択的に表示するために使用することもできます。

次に例を示します。

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>はオーバーライドされ、 `shouldRender` 最初はコンポーネントが読み込まれるときのフィールドの値に設定され `false` ます。
* このボタンが選択されている場合、 `shouldRender` はに設定され `true` ます。これにより、コンポーネントは、更新されたに強制的にレンダリングされ `currentCount` ます。
* 再リリースの直後に、 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> の値を `shouldRender` に戻して、 `false` 次回ボタンが選択されるまで再実行が行われないようにします。

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

詳細については、「<xref:blazor/lifecycle#after-component-render>」を参照してください。

## <a name="virtualize-re-usable-fragments"></a>再利用可能なフラグメントを仮想化する

コンポーネントは、コードとマークアップの再利用可能なフラグメントを生成するための便利なアプローチを提供します。 一般に、アプリの要件に最適な個々のコンポーネントを作成することをお勧めします。 注意点の1つは、追加の子コンポーネントが親コンポーネントのレンダリングにかかる合計時間に影響することです。 ほとんどのアプリでは、追加のオーバーヘッドはほとんどありません。 多数のコンポーネントを生成するアプリでは、表示するコンポーネントの数を制限するなど、処理のオーバーヘッドを減らすために戦略を使用することを検討する必要があります。

たとえば、コンポーネントを含む数百の行を表示するグリッドまたはリストは、レンダリングにプロセッサを使用します。 コンポーネントのサブセットのみが特定の時点でレンダリングされるように、グリッドまたはリストのレイアウトを仮想化することを検討してください。 コンポーネントのサブセットレンダリングの例については、[仮想化サンプルアプリの次のコンポーネント (aspnet/Samples GitHub リポジトリ)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)を参照してください。

* `Virtualize`コンポーネント ([共有/仮想](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)化): <xref:Microsoft.AspNetCore.Components.ComponentBase> ユーザーのスクロールに基づいて一連の天気データ行を表示するためにを実装する、C# で記述されたコンポーネント。
* `FetchData`コンポーネント ([Pages/fetchdata. razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): コンポーネントを使用して `Virtualize` 、一度に25行の気象データを表示します。

## <a name="avoid-javascript-interop-to-marshal-data"></a>データをマーシャリングするための JavaScript の相互運用を回避する

BlazorWebassembly は、JavaScript (JS) 相互運用呼び出しは webassembly 境界をたどる必要があります。 2つのコンテキストでコンテンツをシリアル化または逆シリアル化すると、アプリの処理オーバーヘッドが発生します。 多くの場合、JS 相互運用の呼び出しがパフォーマンスに悪影響を与えることがあります。 境界を越えたデータのマーシャリングを減らすには、アプリが多数の小さなペイロードを1つの大きなペイロードに統合できるかどうかを判断して、WebAssembly JS の間でコンテキストを頻繁に切り替えることがないようにします。

## <a name="use-systemtextjson"></a>System.string を使用する

Blazorの JS 相互運用機能の実装 <xref:System.Text.Json> は、メモリ割り当てが少ない高パフォーマンスの JSON シリアル化ライブラリであるに依存しています。 を使用して <xref:System.Text.Json> も、1つまたは複数の代替 JSON ライブラリを追加しても、アプリペイロードのサイズは追加されません。

移行のガイダンスについては、「 [Newtonsoft. json から system.string に移行する方法](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)」を参照してください。

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>必要に応じて、同期およびマーシャリング解除された JS 相互運用 Api を使用する

Blazor<xref:Microsoft.JSInterop.IJSRuntime>サーバーアプリで利用可能な1つのバージョンでは、2つの追加バージョンのが提供されてい Blazor ます。

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>JS 相互運用呼び出しを同期的に呼び出すことができます。この場合、非同期バージョンよりもオーバーヘッドが少なくなります。

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>マーシャリング解除した JS 相互運用呼び出しを許可します。

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
  > を使用すると、 <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> JS 相互運用アプローチのオーバーヘッドが最小限になりますが、これらの api を操作するために必要な JavaScript api は現在ドキュメントに記載されていません。将来のリリースでは、互換性に影響する変更の対象となります。

## <a name="reduce-app-size"></a>アプリのサイズを小さくする

### <a name="intermediate-language-il-linking"></a>中間言語 (IL) リンク

[リンク Blazorアプリの](xref:host-and-deploy/blazor/configure-linker)バイナリで使用されていないコードをトリミングすることで、アプリのサイズを縮小できます。 既定では、リンカーは、構成でビルドするときにのみ有効になり `Release` ます。 これを活用するには、 [dotnet publish](/dotnet/core/tools/dotnet-publish)コマンドを使用して、 [-c |--構成](/dotnet/core/tools/dotnet-publish#options)オプションをに設定して、デプロイ用にアプリを発行し `Release` ます。

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a>使用されていない機能を無効にする

BlazorWebAssembly ランタイムには、次の .NET 機能が含まれています。これらの機能は、アプリがペイロードサイズを小さくする必要がない場合に無効にすることができます。

* データファイルは、タイムゾーン情報を修正するために含まれています。 アプリがこの機能を必要としない場合は、 `BlazorEnableTimeZoneSupport` アプリのプロジェクトファイルの MSBuild プロパティを次のように設定して無効にすることを検討してください `false` 。

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* 照合順序情報は、などの Api を正常に動作させるために含まれてい <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> ます。 アプリが照合順序データを必要としないことがわかっている場合は、 `BlazorWebAssemblyPreserveCollationData` アプリのプロジェクトファイルの MSBuild プロパティを次のように設定して無効にすることを検討してください `false` 。

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
