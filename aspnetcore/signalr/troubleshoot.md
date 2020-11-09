---
title: 'ASP.NET Core SignalR 接続のトラブルシューティング'
author: bradygaster
description: SignalR接続のトラブルシューティングを ASP.NET Core します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059612"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="97efd-103">接続エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="97efd-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="97efd-104">このセクションでは、ASP.NET Core ハブへの接続を確立しようとしたときに発生する可能性があるエラーについて説明 SignalR します。</span><span class="sxs-lookup"><span data-stu-id="97efd-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="97efd-105">応答コード404</span><span class="sxs-lookup"><span data-stu-id="97efd-105">Response code 404</span></span>

<span data-ttu-id="97efd-106">Websocket およびを使用する場合 `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="97efd-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="97efd-107">固定セッションを使用せずに複数のサーバーを使用する場合は、1台のサーバーで接続を開始してから別のサーバーに切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="97efd-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="97efd-108">もう一方のサーバーは、以前の接続を認識していません。</span><span class="sxs-lookup"><span data-stu-id="97efd-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="97efd-109">クライアントが正しいエンドポイントに接続していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="97efd-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="97efd-110">たとえば、サーバーがでホストされ、 `http://127.0.0.1:5000/hub/myHub` クライアントがに接続しようとしているとし `http://127.0.0.1:5000/myHub` ます。</span><span class="sxs-lookup"><span data-stu-id="97efd-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="97efd-111">接続で ID が使用され、ネゴシエート後にサーバーに要求を送信するのに時間がかかりすぎる場合、サーバーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="97efd-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="97efd-112">ID を削除します。</span><span class="sxs-lookup"><span data-stu-id="97efd-112">Deletes the ID.</span></span>
  * <span data-ttu-id="97efd-113">は404を返します。</span><span class="sxs-lookup"><span data-stu-id="97efd-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="97efd-114">応答コード400または503</span><span class="sxs-lookup"><span data-stu-id="97efd-114">Response code 400 or 503</span></span>

<span data-ttu-id="97efd-115">次のエラーが発生した場合:</span><span class="sxs-lookup"><span data-stu-id="97efd-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="97efd-116">このエラーは通常、Websocket トランスポートのみを使用しているクライアントによって発生しますが、Websocket プロトコルはサーバーで有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="97efd-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="97efd-117">応答コード307</span><span class="sxs-lookup"><span data-stu-id="97efd-117">Response code 307</span></span>

<span data-ttu-id="97efd-118">Websocket およびを使用する場合 `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="97efd-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="97efd-119">このエラーは、negotiate 要求中にも発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="97efd-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="97efd-120">一般的な原因:</span><span class="sxs-lookup"><span data-stu-id="97efd-120">Common cause:</span></span>
* <span data-ttu-id="97efd-121">アプリは、でを呼び出して HTTPS を強制する `UseHttpsRedirection` `Startup` か、URL 書き換えルールを使用して https を強制するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="97efd-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="97efd-122">解決方法:</span><span class="sxs-lookup"><span data-stu-id="97efd-122">Possible solution:</span></span>
* <span data-ttu-id="97efd-123">クライアント側の URL を "http" から "https" に変更します。</span><span class="sxs-lookup"><span data-stu-id="97efd-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="97efd-124">応答コード405</span><span class="sxs-lookup"><span data-stu-id="97efd-124">Response code 405</span></span>

<span data-ttu-id="97efd-125">Http 状態コード 405-メソッドは使用できません</span><span class="sxs-lookup"><span data-stu-id="97efd-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="97efd-126">アプリで [CORS](xref:signalr/security#cross-origin-resource-sharing) が有効になっていません</span><span class="sxs-lookup"><span data-stu-id="97efd-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="97efd-127">応答コード0</span><span class="sxs-lookup"><span data-stu-id="97efd-127">Response code 0</span></span>

<span data-ttu-id="97efd-128">Http 状態コード 0-通常は [CORS](xref:signalr/security#cross-origin-resource-sharing) の問題です。状態コードは指定されていません。</span><span class="sxs-lookup"><span data-stu-id="97efd-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="97efd-129">予想されるオリジンをに追加します。 `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="97efd-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="97efd-130">`.AllowCredentials()`CORS ポリシーにを追加します。</span><span class="sxs-lookup"><span data-stu-id="97efd-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="97efd-131">`.AllowAnyOrigin()`またはを `.WithOrigins("*")` このオプションと共に使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="97efd-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="97efd-132">応答コード413</span><span class="sxs-lookup"><span data-stu-id="97efd-132">Response code 413</span></span>

<span data-ttu-id="97efd-133">Http 状態コード 413-ペイロードが大きすぎます</span><span class="sxs-lookup"><span data-stu-id="97efd-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="97efd-134">多くの場合、アクセストークンが4k を超えていることが原因です。</span><span class="sxs-lookup"><span data-stu-id="97efd-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="97efd-135">Azure サービスを使用している場合は、 SignalR サービスを介して送信される要求を次のようにカスタマイズすることによって、トークンサイズを減らします。</span><span class="sxs-lookup"><span data-stu-id="97efd-135">If using the Azure SignalR Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="97efd-136">一時的なネットワークエラー</span><span class="sxs-lookup"><span data-stu-id="97efd-136">Transient network failures</span></span>

<span data-ttu-id="97efd-137">一時的なネットワークエラーによって接続が閉じられる場合があり SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="97efd-137">Transient network failures may close the SignalR connection.</span></span> <span data-ttu-id="97efd-138">サーバーは、終了した接続を正常なクライアント切断として解釈する場合があります。</span><span class="sxs-lookup"><span data-stu-id="97efd-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="97efd-139">クライアントが切断された理由に関する詳細情報を取得するには [、クライアントとサーバーからログを収集](xref:signalr/diagnostics)します。</span><span class="sxs-lookup"><span data-stu-id="97efd-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>