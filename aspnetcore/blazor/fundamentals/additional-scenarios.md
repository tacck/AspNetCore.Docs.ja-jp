---
title: ASP.NET Core Blazor ホスティング モデルの構成
author: guardrex
description: ASP.NET Core Blazor ホスティング モデルの構成におけるその他のシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 6f092f3f9a18883c31b217b59d0b0abe802aff01
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628302"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="67de5-103">ASP.NET Core Blazor ホスティング モデルの構成</span><span class="sxs-lookup"><span data-stu-id="67de5-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="67de5-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Mackinnon Buck](https://github.com/MackinnonBuck)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="67de5-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="67de5-105">この記事では、ホスティング モデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="67de5-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="67de5-106">認証のための SignalR のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="67de5-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="67de5-107">*このセクションは Blazor WebAssembly に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="67de5-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="67de5-108">cookie や HTTP 認証ヘッダーなどの資格情報を送信するように SignalR の基となるクライアントを構成するには:</span><span class="sxs-lookup"><span data-stu-id="67de5-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="67de5-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> を使用して、クロスオリジン [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求に <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> を設定します。</span><span class="sxs-lookup"><span data-stu-id="67de5-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="67de5-110"><xref:System.Net.Http.HttpMessageHandler> を <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> オプションに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="67de5-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="67de5-111">詳細については、「<xref:signalr/configuration#configure-additional-options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="67de5-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="67de5-112">UI に接続状態を反映する</span><span class="sxs-lookup"><span data-stu-id="67de5-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="67de5-113">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="67de5-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="67de5-114">接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="67de5-115">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="67de5-116">UI をカスタマイズするには、`_Host.cshtml` Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="67de5-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="67de5-117">アプリのスタイルシート (`wwwroot/css/app.css` または `wwwroot/css/site.css`) に以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="67de5-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="67de5-118">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="67de5-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="67de5-119">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="67de5-119">CSS class</span></span>                       | <span data-ttu-id="67de5-120">示す内容&hellip;</span><span class="sxs-lookup"><span data-stu-id="67de5-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="67de5-121">接続が失われました。</span><span class="sxs-lookup"><span data-stu-id="67de5-121">A lost connection.</span></span> <span data-ttu-id="67de5-122">クライアントによって再接続が試行されています。</span><span class="sxs-lookup"><span data-stu-id="67de5-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="67de5-123">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="67de5-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="67de5-124">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="67de5-125">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="67de5-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="67de5-126">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="67de5-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="67de5-127">再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="67de5-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="67de5-128">再接続が拒否されました。</span><span class="sxs-lookup"><span data-stu-id="67de5-128">Reconnection rejected.</span></span> <span data-ttu-id="67de5-129">サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。</span><span class="sxs-lookup"><span data-stu-id="67de5-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="67de5-130">アプリを再度読み込むには、`location.reload()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="67de5-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="67de5-131">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="67de5-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="67de5-132">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="67de5-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="67de5-133">クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。</span><span class="sxs-lookup"><span data-stu-id="67de5-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="67de5-134">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="67de5-135">サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</span><span class="sxs-lookup"><span data-stu-id="67de5-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="67de5-136">表示モード</span><span class="sxs-lookup"><span data-stu-id="67de5-136">Render mode</span></span>

<span data-ttu-id="67de5-137">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="67de5-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="67de5-138">Blazor Server アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="67de5-139">これは `_Host.cshtml` Razor ページで設定します。</span><span class="sxs-lookup"><span data-stu-id="67de5-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="67de5-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="67de5-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="67de5-141">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="67de5-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="67de5-142">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="67de5-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="67de5-143">表示モード</span><span class="sxs-lookup"><span data-stu-id="67de5-143">Render mode</span></span> | <span data-ttu-id="67de5-144">説明</span><span class="sxs-lookup"><span data-stu-id="67de5-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="67de5-145">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="67de5-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="67de5-146">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="67de5-147">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="67de5-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="67de5-148">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="67de5-148">Output from the component isn't included.</span></span> <span data-ttu-id="67de5-149">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="67de5-150">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="67de5-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="67de5-151">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="67de5-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="67de5-152">Blazor Server アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="67de5-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="67de5-153">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="67de5-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="67de5-154">`Pages/_Host.cshtml` ファイルで Blazor Server アプリによって使用される SignalR クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="67de5-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="67de5-155">`Blazor.start` を呼び出すスクリプトを、`_framework/blazor.server.js` スクリプトの後の `</body>` タグ内に配置します。</span><span class="sxs-lookup"><span data-stu-id="67de5-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="67de5-156">ログの記録</span><span class="sxs-lookup"><span data-stu-id="67de5-156">Logging</span></span>

<span data-ttu-id="67de5-157">SignalR クライアント ログを構成するには:</span><span class="sxs-lookup"><span data-stu-id="67de5-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="67de5-158">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="67de5-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="67de5-159">クライアント ビルダーでログ レベルを指定して `configureLogging` を呼び出す構成オブジェクト (`configureSignalR`) を渡します。</span><span class="sxs-lookup"><span data-stu-id="67de5-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

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

<span data-ttu-id="67de5-160">前の例で、`information` はログ レベル <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> と同じです。</span><span class="sxs-lookup"><span data-stu-id="67de5-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="67de5-161">再接続ハンドラーを変更する</span><span class="sxs-lookup"><span data-stu-id="67de5-161">Modify the reconnection handler</span></span>

<span data-ttu-id="67de5-162">再接続ハンドラーの回線接続イベントは、次のようなカスタム動作を行うように変更できます。</span><span class="sxs-lookup"><span data-stu-id="67de5-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="67de5-163">接続が切断された場合にユーザーに通知する。</span><span class="sxs-lookup"><span data-stu-id="67de5-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="67de5-164">回線が接続されているときに (クライアントから) ログ記録を実行する。</span><span class="sxs-lookup"><span data-stu-id="67de5-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="67de5-165">接続イベントを変更するには:</span><span class="sxs-lookup"><span data-stu-id="67de5-165">To modify the connection events:</span></span>

* <span data-ttu-id="67de5-166">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="67de5-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="67de5-167">切断された接続 (`onConnectionDown`) および確立または再確立された接続 (`onConnectionUp`) 用に接続の変更に対するコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="67de5-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="67de5-168">`onConnectionDown` と `onConnectionUp` の**両方**を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="67de5-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="67de5-169">再接続の再試行回数と間隔を調整する</span><span class="sxs-lookup"><span data-stu-id="67de5-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="67de5-170">再接続の再試行回数と間隔を調整するには:</span><span class="sxs-lookup"><span data-stu-id="67de5-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="67de5-171">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="67de5-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="67de5-172">再試行の回数 (`maxRetries`) と、各再試行で許可されるミリ秒単位の期間 (`retryIntervalMilliseconds`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="67de5-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="67de5-173">再接続の表示を非表示にする、または置き換える</span><span class="sxs-lookup"><span data-stu-id="67de5-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="67de5-174">再接続の表示を非表示にするには:</span><span class="sxs-lookup"><span data-stu-id="67de5-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="67de5-175">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="67de5-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="67de5-176">再接続ハンドラーの `_reconnectionDisplay` を空のオブジェクト (`{}` または `new Object()`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="67de5-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

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

<span data-ttu-id="67de5-177">再接続の表示を置き換えるには、前の例の `_reconnectionDisplay` を表示する要素に設定します。</span><span class="sxs-lookup"><span data-stu-id="67de5-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="67de5-178">プレースホルダー `{ELEMENT ID}` は、表示する HTML 要素の ID です。</span><span class="sxs-lookup"><span data-stu-id="67de5-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="67de5-179">HTML `<head>` タグ要素に影響を与える</span><span class="sxs-lookup"><span data-stu-id="67de5-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="67de5-180">*このセクションは、今後予定されている、Blazor WebAssembly と Blazor Server の ASP.NET Core 5.0 リリースに適用されます。*</span><span class="sxs-lookup"><span data-stu-id="67de5-180">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="67de5-181">レンダリングされるときに、`Title`、`Link`、および `Meta` コンポーネントによって HTML `<head>` タグ要素のデータが追加または更新されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="67de5-182">前の例では、`{TITLE}`、`{URL}`、および `{DESCRIPTION}` のプレースホルダーは、文字列値、Razor 変数、または Razor 式です。</span><span class="sxs-lookup"><span data-stu-id="67de5-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="67de5-183">次の特徴があります。</span><span class="sxs-lookup"><span data-stu-id="67de5-183">The following characteristics apply:</span></span>

* <span data-ttu-id="67de5-184">サーバー側の事前レンダリングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="67de5-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="67de5-185">`Value` パラメーターは、`Title` コンポーネントの唯一の有効なパラメーターです。</span><span class="sxs-lookup"><span data-stu-id="67de5-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="67de5-186">`Meta` および `Link` コンポーネントに提供されている HTML 属性は、[追加の属性](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters)にキャプチャされ、レンダリングされた HTML タグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="67de5-187">複数の `Title` コンポーネントの場合、ページのタイトルには、レンダリングされた最後の `Title` コンポーネントの `Value` が反映されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="67de5-188">複数の `Meta` または `Link` コンポーネントが同一の属性に含まれている場合、`Meta` または `Link` コンポーネントごとに 1 つの HTML タグがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="67de5-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="67de5-189">2 つの `Meta` または `Link` コンポーネントが、同じレンダリングされた HTML タグを参照することはできません。</span><span class="sxs-lookup"><span data-stu-id="67de5-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="67de5-190">既存の `Meta` または `Link` コンポーネントのパラメーターに対する変更は、それらのレンダリングされる HTML タグに反映されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="67de5-191">`Link` または `Meta` コンポーネントがレンダリングされなくなり、フレームワークによって破棄されると、それらのレンダリングされた HTML タグは削除されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="67de5-192">フレームワーク コンポーネントの 1 つが子コンポーネントで使用されている場合、そのフレームワーク コンポーネントを含む子コンポーネントがレンダリングされていれば、レンダリングされた HTML タグは親コンポーネントのその他の子コンポーネントに影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="67de5-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="67de5-193">子コンポーネントでこれらのフレームワーク コンポーネントの 1 つを使用することと、`wwwroot/index.html` または `Pages/_Host.cshtml` に HTML タグを配置することの違いは、フレームワーク コンポーネントのレンダリングされた HTML タグが次のようになることです。</span><span class="sxs-lookup"><span data-stu-id="67de5-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="67de5-194">アプリケーションの状態によって変更できます。</span><span class="sxs-lookup"><span data-stu-id="67de5-194">Can be modified by application state.</span></span> <span data-ttu-id="67de5-195">ハードコーディングされた HTML タグは、アプリケーションの状態によって変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="67de5-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="67de5-196">親コンポーネントがレンダリングされなくなると、HTML `<head>` から削除されます。</span><span class="sxs-lookup"><span data-stu-id="67de5-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

## <a name="static-files"></a><span data-ttu-id="67de5-197">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="67de5-197">Static files</span></span>

<span data-ttu-id="67de5-198">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="67de5-198">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="67de5-199"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> を使用して追加のファイル マッピングを作成するか、他の <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を構成するには、次の方法のうち **1 つ**を使用します。</span><span class="sxs-lookup"><span data-stu-id="67de5-199">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="67de5-200">次の例では、`{EXTENSION}` プレースホルダーはファイル拡張子、`{CONTENT TYPE}` プレースホルダーはコンテンツ タイプです。</span><span class="sxs-lookup"><span data-stu-id="67de5-200">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="67de5-201"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を使用して `Startup.ConfigureServices` (`Startup.cs`) で[依存関係の挿入 (DI)](xref:blazor/fundamentals/dependency-injection) でオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="67de5-201">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="67de5-202">この方法では `blazor.server.js` の処理に使用するのと同じファイル プロバイダーが構成されるため、カスタム構成が `blazor.server.js` の提供に干渉しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="67de5-202">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="67de5-203">たとえば、`provider.Mappings.Remove(".js")` でプロバイダーを構成することによって、JavaScript ファイルのマッピングを削除しないでください。</span><span class="sxs-lookup"><span data-stu-id="67de5-203">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="67de5-204">`Startup.Configure` (`Startup.cs`) で <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> の 2 つの呼び出しを使用します。</span><span class="sxs-lookup"><span data-stu-id="67de5-204">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="67de5-205"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を使用した最初の呼び出しでカスタム ファイル プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="67de5-205">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="67de5-206">2 番目のミドルウェアは、Blazor フレームワークによって提供される既定の静的ファイル構成を使用する `blazor.server.js` に対応します。</span><span class="sxs-lookup"><span data-stu-id="67de5-206">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

## <a name="additional-resources"></a><span data-ttu-id="67de5-207">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="67de5-207">Additional resources</span></span>

* <xref:fundamentals/logging/index>
