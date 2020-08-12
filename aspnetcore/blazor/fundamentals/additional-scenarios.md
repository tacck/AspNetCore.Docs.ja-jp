---
title: ASP.NET Core Blazor ホスティング モデルの構成
author: guardrex
description: ASP.NET Core Blazor ホスティング モデルの構成におけるその他のシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b32710e515d111b7dd6556f1db55082cd56a82b5
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819003"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>ASP.NET Core Blazor ホスティング モデルの構成

作成者: [Daniel Roth](https://github.com/danroth27)、[Mackinnon Buck](https://github.com/MackinnonBuck)、[Luke Latham](https://github.com/guardrex)

この記事では、ホスティング モデルの構成について説明します。

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>認証のための SignalR のクロスオリジンネゴシエーション

*このセクションは Blazor WebAssembly に適用されます。*

Cookie や HTTP 認証ヘッダーなどの資格情報を送信するように SignalR の基となるクライアントを構成するには:

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

*このセクションは Blazor Server に適用されます。*

Blazor Server アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。 これは `_Host.cshtml` Razor ページで設定します。

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> によって、コンポーネントに対して以下の構成が行われます。

* ページに事前レンダリングするかどうか。
* ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。

| 表示モード | 説明 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server アプリのマーカーをレンダリングします。 コンポーネントからの出力は含められません。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | コンポーネントを静的 HTML にレンダリングします。 |

静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a>Blazor Server アプリ用に SignalR クライアントを構成する

*このセクションは Blazor Server に適用されます。*

`Pages/_Host.cshtml` ファイルで Blazor Server アプリによって使用される SignalR クライアントを構成します。 `Blazor.start` を呼び出すスクリプトを、`_framework/blazor.server.js` スクリプトの後の `</body>` タグ内に配置します。

### <a name="logging"></a>ログの記録

SignalR クライアント ログを構成するには:

* `blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* クライアント ビルダーでログ レベルを指定して `configureLogging` を呼び出す構成オブジェクト (`configureSignalR`) を渡します。

```cshtml
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

接続イベントを変更するには:

* `blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* 切断された接続 (`onConnectionDown`) および確立または再確立された接続 (`onConnectionUp`) 用に接続の変更に対するコールバックを登録します。 `onConnectionDown` と `onConnectionUp` の**両方**を指定する必要があります。

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>再接続の再試行回数と間隔を調整する

再接続の再試行回数と間隔を調整するには:

* `blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* 再試行の回数 (`maxRetries`) と、各再試行で許可されるミリ秒単位の期間 (`retryIntervalMilliseconds`) を設定します。

```cshtml
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

### <a name="hide-or-replace-the-reconnection-display"></a>再接続の表示を非表示にする、または置き換える

再接続の表示を非表示にするには:

* `blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* 再接続ハンドラーの `_reconnectionDisplay` を空のオブジェクト (`{}` または `new Object()`) に設定します。

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
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

## <a name="influence-html-head-tag-elements"></a>HTML `<head>` タグ要素に影響を与える

*このセクションは Blazor WebAssembly および Blazor Server に適用されます。*

レンダリングされるときに、`Title`、`Link`、および `Meta` コンポーネントによって HTML `<head>` タグ要素のデータが追加または更新されます。

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

前の例では、`{TITLE}`、`{URL}`、および `{DESCRIPTION}` のプレースホルダーは、文字列値、Razor 変数、または Razor 式です。

次の特徴があります。

* サーバー側の事前レンダリングがサポートされています。
* `Value` パラメーターは、`Title` コンポーネントの唯一の有効なパラメーターです。
* `Meta` および `Link` コンポーネントに提供されている HTML 属性は、[追加の属性](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters)にキャプチャされ、レンダリングされた HTML タグに渡されます。
* 複数の `Title` コンポーネントの場合、ページのタイトルには、レンダリングされた最後の `Title` コンポーネントの `Value` が反映されます。
* 複数の `Meta` または `Link` コンポーネントが同一の属性に含まれている場合、`Meta` または `Link` コンポーネントごとに 1 つの HTML タグがレンダリングされます。 2 つの `Meta` または `Link` コンポーネントが、同じレンダリングされた HTML タグを参照することはできません。
* 既存の `Meta` または `Link` コンポーネントのパラメーターに対する変更は、それらのレンダリングされる HTML タグに反映されます。
* `Link` または `Meta` コンポーネントがレンダリングされなくなり、フレームワークによって破棄されると、それらのレンダリングされた HTML タグは削除されます。

フレームワーク コンポーネントの 1 つが子コンポーネントで使用されている場合、そのフレームワーク コンポーネントを含む子コンポーネントがレンダリングされていれば、レンダリングされた HTML タグは親コンポーネントのその他の子コンポーネントに影響を与えます。 子コンポーネントでこれらのフレームワーク コンポーネントの 1 つを使用することと、`wwwroot/index.html` または `Pages/_Host.cshtml` に HTML タグを配置することの違いは、フレームワーク コンポーネントのレンダリングされた HTML タグが次のようになることです。

* アプリケーションの状態によって変更できます。 ハードコーディングされた HTML タグは、アプリケーションの状態によって変更することはできません。
* 親コンポーネントがレンダリングされなくなると、HTML `<head>` から削除されます。

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/logging/index>
