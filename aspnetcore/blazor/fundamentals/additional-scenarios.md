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
ms.openlocfilehash: ef37c539d377f14a2744c3ead28234d8497df700
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637679"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="ccaa7-103">ASP.NET Core Blazor ホスティング モデルの構成</span><span class="sxs-lookup"><span data-stu-id="ccaa7-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="ccaa7-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Mackinnon Buck](https://github.com/MackinnonBuck)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ccaa7-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ccaa7-105">この記事では、ホスティング モデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="ccaa7-106">認証のための SignalR のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="ccaa7-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="ccaa7-107">*このセクションは Blazor WebAssembly に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="ccaa7-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="ccaa7-108">cookie や HTTP 認証ヘッダーなどの資格情報を送信するように SignalR の基となるクライアントを構成するには:</span><span class="sxs-lookup"><span data-stu-id="ccaa7-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="ccaa7-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> を使用して、クロスオリジン [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求に <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> を設定します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="ccaa7-110"><xref:System.Net.Http.HttpMessageHandler> を <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> オプションに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="ccaa7-111">詳細については、「<xref:signalr/configuration#configure-additional-options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="ccaa7-112">UI に接続状態を反映する</span><span class="sxs-lookup"><span data-stu-id="ccaa7-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="ccaa7-113">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="ccaa7-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ccaa7-114">接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ccaa7-115">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="ccaa7-116">UI をカスタマイズするには、`_Host.cshtml` Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="ccaa7-117">アプリのスタイルシート (`wwwroot/css/app.css` または `wwwroot/css/site.css`) に以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="ccaa7-118">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="ccaa7-119">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="ccaa7-119">CSS class</span></span>                       | <span data-ttu-id="ccaa7-120">示す内容&hellip;</span><span class="sxs-lookup"><span data-stu-id="ccaa7-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="ccaa7-121">接続が失われました。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-121">A lost connection.</span></span> <span data-ttu-id="ccaa7-122">クライアントによって再接続が試行されています。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="ccaa7-123">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="ccaa7-124">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="ccaa7-125">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="ccaa7-126">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="ccaa7-127">再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="ccaa7-128">再接続が拒否されました。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-128">Reconnection rejected.</span></span> <span data-ttu-id="ccaa7-129">サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="ccaa7-130">アプリを再度読み込むには、`location.reload()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="ccaa7-131">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="ccaa7-132">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="ccaa7-133">クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="ccaa7-134">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="ccaa7-135">サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="ccaa7-136">表示モード</span><span class="sxs-lookup"><span data-stu-id="ccaa7-136">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ccaa7-137">*このセクションは、ホストされている Blazor WebAssembly および Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="ccaa7-137">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="ccaa7-138">Blazor アプリは既定では、サーバー上の UI をプリレンダリングするように設定されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-138">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="ccaa7-139">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-139">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ccaa7-140">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="ccaa7-140">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ccaa7-141">Blazor Server アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-141">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ccaa7-142">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-142">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="ccaa7-143">Blazor 回線を初期化する</span><span class="sxs-lookup"><span data-stu-id="ccaa7-143">Initialize the Blazor circuit</span></span>

<span data-ttu-id="ccaa7-144">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="ccaa7-144">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ccaa7-145">`Pages/_Host.cshtml` ファイル内にある Blazor Server アプリの [SignalR 回線](xref:blazor/hosting-models#circuits)の手動での起動を構成します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-145">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="ccaa7-146">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-146">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ccaa7-147">`Blazor.start` を呼び出すスクリプトを、`blazor.server.js` スクリプトのタグの後の終了 `</body>` タグ内に配置します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-147">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="ccaa7-148">`autostart` が無効になっている場合、回線に依存しないアプリのすべての側面が正常に動作します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-148">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="ccaa7-149">たとえば、クライアント側のルーティングは動作します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-149">For example, client-side routing is operational.</span></span> <span data-ttu-id="ccaa7-150">ただし、回線に依存する側面はすべて、`Blazor.start` が呼び出されるまで動作しません。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-150">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="ccaa7-151">回線が確立されていなければ、アプリの動作は予測不可能です。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-151">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="ccaa7-152">たとえば、回線が切断されている間、コンポーネント メソッドは実行できません。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-152">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="ccaa7-153">ドキュメントの準備完了時に Blazor を初期化する</span><span class="sxs-lookup"><span data-stu-id="ccaa7-153">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="ccaa7-154">ドキュメントの準備完了時に Blazor アプリを初期化するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-154">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="ccaa7-155">手動で起動した結果として得た `Promise` に連結する</span><span class="sxs-lookup"><span data-stu-id="ccaa7-155">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="ccaa7-156">JS 相互運用機能の初期化など、追加のタスクを実行するには、`then` を使用して、手動で Blazor アプリを起動した結果として得た `Promise` に連結します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-156">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="ccaa7-157">SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="ccaa7-157">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="ccaa7-158">ログの記録</span><span class="sxs-lookup"><span data-stu-id="ccaa7-158">Logging</span></span>

<span data-ttu-id="ccaa7-159">SignalR クライアント ログを構成するには、クライアント ビルダーでログ レベルを指定して `configureLogging` を呼び出す構成オブジェクト (`configureSignalR`) を渡します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-159">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="ccaa7-160">前の例で、`information` はログ レベル <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> と同じです。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="ccaa7-161">再接続ハンドラーを変更する</span><span class="sxs-lookup"><span data-stu-id="ccaa7-161">Modify the reconnection handler</span></span>

<span data-ttu-id="ccaa7-162">再接続ハンドラーの回線接続イベントは、次のようなカスタム動作を行うように変更できます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="ccaa7-163">接続が切断された場合にユーザーに通知する。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="ccaa7-164">回線が接続されているときに (クライアントから) ログ記録を実行する。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="ccaa7-165">接続イベントを変更するには、次の接続の変更に対してコールバックを登録します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-165">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="ccaa7-166">切断された接続では、`onConnectionDown` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-166">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="ccaa7-167">確立または再確立された接続では、`onConnectionUp` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-167">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="ccaa7-168">`onConnectionDown` と `onConnectionUp` の **両方** を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="ccaa7-169">再接続の再試行回数と間隔を調整する</span><span class="sxs-lookup"><span data-stu-id="ccaa7-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="ccaa7-170">再接続の再試行の回数と間隔を調整するには、再試行の回数 (`maxRetries`) と、各再試行で許可されるミリ秒単位の期間 (`retryIntervalMilliseconds`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-170">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="ccaa7-171">再接続の表示を非表示にする、または置き換える</span><span class="sxs-lookup"><span data-stu-id="ccaa7-171">Hide or replace the reconnection display</span></span>

<span data-ttu-id="ccaa7-172">再接続の表示を非表示にするには、再接続ハンドラーの `_reconnectionDisplay` を空のオブジェクト (`{}` または `new Object()`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-172">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="ccaa7-173">再接続の表示を置き換えるには、前の例の `_reconnectionDisplay` を表示する要素に設定します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-173">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="ccaa7-174">プレースホルダー `{ELEMENT ID}` は、表示する HTML 要素の ID です。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-174">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ccaa7-175">モーダル要素に対して、アプリの CSS (`wwwroot/css/site.css`) の `transition-delay` プロパティを設定して、再接続表示が表示されるまでの遅延時間をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-175">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="ccaa7-176">次の例では、移行遅延時間を 500 ミリ秒 (既定値) から 1,000 ミリ秒 (1 秒) に設定しています。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-176">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="ccaa7-177">クライアントから Blazor 回線を切断する</span><span class="sxs-lookup"><span data-stu-id="ccaa7-177">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="ccaa7-178">既定では、[`unload` ページ イベント](https://developer.mozilla.org/docs/Web/API/Window/unload_event)がトリガーされると、Blazor 回線が切断されます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-178">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="ccaa7-179">クライアント上の他のシナリオで回線を切断するには、適切なイベント ハンドラーで `Blazor.disconnect` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-179">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="ccaa7-180">次の例では、ページが非表示になると、回線が切断されます ([`pagehide` イベント](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event))。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-180">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

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

## <a name="static-files"></a><span data-ttu-id="ccaa7-181">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="ccaa7-181">Static files</span></span>

<span data-ttu-id="ccaa7-182">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="ccaa7-182">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ccaa7-183"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> を使用して追加のファイル マッピングを作成するか、他の <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を構成するには、次の方法のうち **1 つ** を使用します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-183">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="ccaa7-184">次の例では、`{EXTENSION}` プレースホルダーはファイル拡張子、`{CONTENT TYPE}` プレースホルダーはコンテンツ タイプです。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-184">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="ccaa7-185"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を使用して `Startup.ConfigureServices` (`Startup.cs`) で[依存関係の挿入 (DI)](xref:blazor/fundamentals/dependency-injection) でオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-185">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="ccaa7-186">この方法では `blazor.server.js` の処理に使用するのと同じファイル プロバイダーが構成されるため、カスタム構成が `blazor.server.js` の提供に干渉しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-186">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="ccaa7-187">たとえば、`provider.Mappings.Remove(".js")` でプロバイダーを構成することによって、JavaScript ファイルのマッピングを削除しないでください。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-187">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="ccaa7-188">`Startup.Configure` (`Startup.cs`) で <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> の 2 つの呼び出しを使用します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-188">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="ccaa7-189"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> を使用した最初の呼び出しでカスタム ファイル プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-189">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="ccaa7-190">2 番目のミドルウェアは、Blazor フレームワークによって提供される既定の静的ファイル構成を使用する `blazor.server.js` に対応します。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-190">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="ccaa7-191"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> を使用してカスタムの静的ファイル ミドルウェアを実行することで、`_framework/blazor.server.js` の提供の妨げにならないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="ccaa7-191">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="ccaa7-192">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="ccaa7-192">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* [<span data-ttu-id="ccaa7-193">Blazor Server 再接続イベントとコンポーネント ライフサイクル イベント</span><span class="sxs-lookup"><span data-stu-id="ccaa7-193">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
