---
title: ASP.NET Core Blazor ホスティング モデルの構成
author: guardrex
description: ASP.NET Core Blazor ホスティング モデルの構成におけるその他のシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b7fc3710fe5ad1efba907edf98f590a42e2a83ae
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485876"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>ASP.NET Core Blazor ホスティング モデルの構成

作成者: [Daniel Roth](https://github.com/danroth27)、[Mackinnon Buck](https://github.com/MackinnonBuck)、[Luke Latham](https://github.com/guardrex)

この記事では、ホスティング モデルの構成について説明します。

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>認証のための SignalR のクロスオリジンネゴシエーション

*このセクションは Blazor WebAssembly に適用されます。*

cookie や HTTP 認証ヘッダーなどの資格情報を送信するように SignalR の基となるクライアントを構成するには:

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> を使用して、クロスオリジン [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求に <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> を設定します。

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <xref:System.Net.Http.HttpMessageHandler> を <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> オプションに割り当てます。

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

詳細については、「<xref:signalr/configuration#configure-additional-options>」を参照してください。

## <a name="reflect-the-connection-state-in-the-ui"></a>UI に接続状態を反映する

*このセクションは Blazor Server に適用されます。*

接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。 再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。

UI をカスタマイズするには、`_Host.cshtml` Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

アプリのスタイルシート (`wwwroot/css/app.css` または `wwwroot/css/site.css`) に以下を追加します。

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。

| CSS クラス                       | 示す内容&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 接続が失われました。 クライアントによって再接続が試行されています。 モーダルを表示します。 |
| `components-reconnect-hide`     | サーバーへのアクティブな接続が再確立されます。 モーダルを非表示にします。 |
| `components-reconnect-failed`   | 再接続に失敗しました。ネットワーク障害が原因である可能性があります。 再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。 |
| `components-reconnect-rejected` | 再接続が拒否されました。 サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。 アプリを再度読み込むには、`location.reload()` を呼び出します。 この接続状態は、次の場合に発生する可能性があります。<ul><li>サーバー側回線でクラッシュが発生した場合。</li><li>クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。 ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</li><li>サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</li></ul> |

## <a name="render-mode"></a>表示モード

::: moniker range=">= aspnetcore-5.0"

*このセクションは、ホストされている Blazor WebAssembly および Blazor Server に適用されます。*

Blazor アプリは既定では、サーバー上の UI をプリレンダリングするように設定されます。 詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

*このセクションは Blazor Server に適用されます。*

Blazor Server アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。 詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a>Blazor 回線を初期化する

*このセクションは Blazor Server に適用されます。*

`Pages/_Host.cshtml` ファイル内にある Blazor Server アプリの [SignalR 回線](xref:blazor/hosting-models#circuits)の手動での起動を構成します。

* `blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* `Blazor.start` を呼び出すスクリプトを、`blazor.server.js` スクリプトのタグの後の終了 `</body>` タグ内に配置します。

`autostart` が無効になっている場合、回線に依存しないアプリのすべての側面が正常に動作します。 たとえば、クライアント側のルーティングは動作します。 ただし、回線に依存する側面はすべて、`Blazor.start` が呼び出されるまで動作しません。 回線が確立されていなければ、アプリの動作は予測不可能です。 たとえば、回線が切断されている間、コンポーネント メソッドは実行できません。

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>ドキュメントの準備完了時に Blazor を初期化する

ドキュメントの準備完了時に Blazor アプリを初期化するには、次のようにします。

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>手動で起動した結果として得た `Promise` に連結する

JS 相互運用機能の初期化など、追加のタスクを実行するには、`then` を使用して、手動で Blazor アプリを起動した結果として得た `Promise` に連結します。

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a>SignalR クライアントを構成する

#### <a name="logging"></a>ログの記録

SignalR クライアント ログを構成するには、クライアント ビルダーでログ レベルを指定して `configureLogging` を呼び出す構成オブジェクト (`configureSignalR`) を渡します。

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

前の例で、`information` はログ レベル <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> と同じです。

### <a name="modify-the-reconnection-handler"></a>再接続ハンドラーを変更する

再接続ハンドラーの回線接続イベントは、次のようなカスタム動作を行うように変更できます。

* 接続が切断された場合にユーザーに通知する。
* 回線が接続されているときに (クライアントから) ログ記録を実行する。

接続イベントを変更するには、次の接続の変更に対してコールバックを登録します。

* 切断された接続では、`onConnectionDown` が使用されます。
* 確立または再確立された接続では、`onConnectionUp` が使用されます。

`onConnectionDown` と `onConnectionUp` の **両方** を指定する必要があります。

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>再接続の再試行回数と間隔を調整する

再接続の再試行の回数と間隔を調整するには、再試行の回数 (`maxRetries`) と、各再試行で許可されるミリ秒単位の期間 (`retryIntervalMilliseconds`) を設定します。

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a>再接続の表示を非表示にする、または置き換える

再接続の表示を非表示にするには、再接続ハンドラーの `_reconnectionDisplay` を空のオブジェクト (`{}` または `new Object()`) に設定します。

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

再接続の表示を置き換えるには、前の例の `_reconnectionDisplay` を表示する要素に設定します。

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

プレースホルダー `{ELEMENT ID}` は、表示する HTML 要素の ID です。

::: moniker range=">= aspnetcore-5.0"

モーダル要素に対して、アプリの CSS (`wwwroot/css/site.css`) の `transition-delay` プロパティを設定して、再接続表示が表示されるまでの遅延時間をカスタマイズします。 次の例では、移行遅延時間を 500 ミリ秒 (既定値) から 1,000 ミリ秒 (1 秒) に設定しています。

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>クライアントから Blazor 回線を切断する

既定では、[`unload` ページ イベント](https://developer.mozilla.org/docs/Web/API/Window/unload_event)がトリガーされると、Blazor 回線が切断されます。 クライアント上の他のシナリオで回線を切断するには、適切なイベント ハンドラーで `Blazor.disconnect` を呼び出します。 次の例では、ページが非表示になると、回線が切断されます ([`pagehide` イベント](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event))。

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="static-files"></a>静的ファイル

*このセクションは Blazor Server に適用されます。*

<xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> を使用して追加のファイル マッピングを作成するか、他の <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を構成するには、次の方法のうち **1 つ** を使用します。 次の例では、`{EXTENSION}` プレースホルダーはファイル拡張子、`{CONTENT TYPE}` プレースホルダーはコンテンツ タイプです。

* <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を使用して `Startup.ConfigureServices` (`Startup.cs`) で[依存関係の挿入 (DI)](xref:blazor/fundamentals/dependency-injection) でオプションを構成します。

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  この方法では `blazor.server.js` の処理に使用するのと同じファイル プロバイダーが構成されるため、カスタム構成が `blazor.server.js` の提供に干渉しないようにしてください。 たとえば、`provider.Mappings.Remove(".js")` でプロバイダーを構成することによって、JavaScript ファイルのマッピングを削除しないでください。

* `Startup.Configure` (`Startup.cs`) で <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> の 2 つの呼び出しを使用します。
  * <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を使用した最初の呼び出しでカスタム ファイル プロバイダーを構成します。
  * 2 番目のミドルウェアは、Blazor フレームワークによって提供される既定の静的ファイル構成を使用する `blazor.server.js` に対応します。

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> を使用してカスタムの静的ファイル ミドルウェアを実行することで、`_framework/blazor.server.js` の提供の妨げにならないようにすることができます。

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/logging/index>
* [Blazor Server 再接続イベントとコンポーネント ライフサイクル イベント](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
