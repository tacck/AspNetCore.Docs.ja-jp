---
title: ASP.NET Core Blazor ホスティング モデルの構成
author: guardrex
description: ASP.NET Core Blazor ホスティング モデルの構成におけるその他のシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: e62cb2ab865fbf57166d5ec3d1344183c00c2095
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059841"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="52316-103">ASP.NET Core Blazor ホスティング モデルの構成</span><span class="sxs-lookup"><span data-stu-id="52316-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="52316-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="52316-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="52316-105">この記事では、ホスティング モデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="52316-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="52316-106">認証のための SignalR のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="52316-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="52316-107">*このセクションは Blazor WebAssembly に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="52316-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="52316-108">Cookie や HTTP 認証ヘッダーなどの資格情報を送信するように SignalR の基となるクライアントを構成するには:</span><span class="sxs-lookup"><span data-stu-id="52316-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="52316-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> を使用して、クロスオリジン [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求に <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> を設定します。</span><span class="sxs-lookup"><span data-stu-id="52316-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="52316-110"><xref:System.Net.Http.HttpMessageHandler> を <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> オプションに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="52316-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="52316-111">詳細については、「<xref:signalr/configuration#configure-additional-options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="52316-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="52316-112">UI に接続状態を反映する</span><span class="sxs-lookup"><span data-stu-id="52316-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="52316-113">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="52316-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="52316-114">接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="52316-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="52316-115">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="52316-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="52316-116">UI をカスタマイズするには、`_Host.cshtml` Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="52316-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="52316-117">アプリのスタイルシート (`wwwroot/css/app.css` または `wwwroot/css/site.css`) に以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="52316-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="52316-118">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="52316-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="52316-119">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="52316-119">CSS class</span></span>                       | <span data-ttu-id="52316-120">示す内容&hellip;</span><span class="sxs-lookup"><span data-stu-id="52316-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="52316-121">接続が失われました。</span><span class="sxs-lookup"><span data-stu-id="52316-121">A lost connection.</span></span> <span data-ttu-id="52316-122">クライアントによって再接続が試行されています。</span><span class="sxs-lookup"><span data-stu-id="52316-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="52316-123">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="52316-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="52316-124">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="52316-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="52316-125">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="52316-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="52316-126">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="52316-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="52316-127">再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="52316-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="52316-128">再接続が拒否されました。</span><span class="sxs-lookup"><span data-stu-id="52316-128">Reconnection rejected.</span></span> <span data-ttu-id="52316-129">サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。</span><span class="sxs-lookup"><span data-stu-id="52316-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="52316-130">アプリを再度読み込むには、`location.reload()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="52316-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="52316-131">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="52316-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="52316-132">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="52316-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="52316-133">クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。</span><span class="sxs-lookup"><span data-stu-id="52316-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="52316-134">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="52316-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="52316-135">サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</span><span class="sxs-lookup"><span data-stu-id="52316-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="52316-136">表示モード</span><span class="sxs-lookup"><span data-stu-id="52316-136">Render mode</span></span>

<span data-ttu-id="52316-137">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="52316-137">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="52316-138"> アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="52316-138"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="52316-139">これは `_Host.cshtml` Razor ページで設定します。</span><span class="sxs-lookup"><span data-stu-id="52316-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="52316-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="52316-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="52316-141">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="52316-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="52316-142">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="52316-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="52316-143">表示モード</span><span class="sxs-lookup"><span data-stu-id="52316-143">Render mode</span></span> | <span data-ttu-id="52316-144">説明</span><span class="sxs-lookup"><span data-stu-id="52316-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="52316-145">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="52316-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="52316-146">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="52316-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="52316-147">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="52316-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="52316-148">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="52316-148">Output from the component isn't included.</span></span> <span data-ttu-id="52316-149">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="52316-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="52316-150">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="52316-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="52316-151">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="52316-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="52316-152">Blazor Server アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="52316-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="52316-153">*このセクションは Blazor Server に適用されます。*</span><span class="sxs-lookup"><span data-stu-id="52316-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="52316-154">場合によっては、Blazor Server アプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="52316-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="52316-155">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成できます。</span><span class="sxs-lookup"><span data-stu-id="52316-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="52316-156">`Pages/_Host.cshtml` ファイルで SignalR クライアントを構成するには:</span><span class="sxs-lookup"><span data-stu-id="52316-156">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="52316-157">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="52316-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="52316-158">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="52316-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="52316-159">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="52316-159">Additional resources</span></span>

* <xref:fundamentals/logging/index>
