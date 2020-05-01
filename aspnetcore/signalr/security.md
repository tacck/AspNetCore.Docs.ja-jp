---
title: ASP.NET Core のセキュリティに関する考慮事項SignalR
author: bradygaster
description: ASP.NET Core SignalRで認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: 5a804425752a26cc5f3185f2b43dd9219dec312d
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558977"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="7a848-103">ASP.NET Core のセキュリティに関する考慮事項SignalR</span><span class="sxs-lookup"><span data-stu-id="7a848-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="7a848-104">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7a848-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="7a848-105">この記事では、のSignalRセキュリティ保護について説明します。</span><span class="sxs-lookup"><span data-stu-id="7a848-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="7a848-106">クロス オリジン リソース共有</span><span class="sxs-lookup"><span data-stu-id="7a848-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="7a848-107">[クロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)を使用して、ブラウザーでクロスオリジンSignalR接続を許可することができます。</span><span class="sxs-lookup"><span data-stu-id="7a848-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="7a848-108">JavaScript コードがアプリとSignalRは別のドメインでホストされている場合、javascript がSignalRアプリに接続できるようにするには、 [CORS ミドルウェア](xref:security/cors)を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="7a848-109">信頼または制御するドメインからのクロスオリジン要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="7a848-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="7a848-110">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7a848-110">For example:</span></span>

* <span data-ttu-id="7a848-111">サイトがホストされている`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="7a848-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="7a848-112">アプリSignalRがホストされている`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="7a848-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="7a848-113">CORS は、オリジンSignalR `www.example.com`のみを許可するようにアプリで構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="7a848-114">CORS の構成の詳細については、「[クロスオリジン要求 (cors) を有効にする](xref:security/cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a848-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="7a848-115">次の CORS ポリシー**が必要**です。</span><span class="sxs-lookup"><span data-stu-id="7a848-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="7a848-116">想定される特定のオリジンを許可します。</span><span class="sxs-lookup"><span data-stu-id="7a848-116">Allow the specific expected origins.</span></span> <span data-ttu-id="7a848-117">配信元を許可することは可能ですが、安全でも推奨され**ません**。</span><span class="sxs-lookup"><span data-stu-id="7a848-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="7a848-118">HTTP メソッド`GET`と`POST`が許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="7a848-119">Cookie ベースの固定セッションが正常に機能するためには、資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="7a848-120">認証が使用されていない場合でも、有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7a848-121">ただし、5.0 では、資格情報を使用しないように TypeScript クライアントでオプションを提供しています。</span><span class="sxs-lookup"><span data-stu-id="7a848-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="7a848-122">資格情報を使用しないオプションは、100% がわかっている場合にのみ使用してください。 Cookie のような資格情報は、アプリでは不要です (複数のサーバーを固定セッションで使用する場合は、azure app service によって cookie が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="7a848-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="7a848-123">たとえば、次の CORS ポリシーでは、 SignalRで`https://example.com`ホストされているブラウザー SignalRクライアントが、 `https://signalr.example.com`でホストされているアプリにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7a848-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="7a848-124">WebSocket 配信元の制限</span><span class="sxs-lookup"><span data-stu-id="7a848-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7a848-125">CORS で提供される保護は、WebSocket には適用されません。</span><span class="sxs-lookup"><span data-stu-id="7a848-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7a848-126">Websocket の配信元の制限については、「 [websocket の配信元の制限](xref:fundamentals/websockets#websocket-origin-restriction)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a848-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7a848-127">CORS で提供される保護は、WebSocket には適用されません。</span><span class="sxs-lookup"><span data-stu-id="7a848-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7a848-128">ブラウザーでは以下を実行**しません**。</span><span class="sxs-lookup"><span data-stu-id="7a848-128">Browsers do **not**:</span></span>

* <span data-ttu-id="7a848-129">CORS の事前要求を実行する。</span><span class="sxs-lookup"><span data-stu-id="7a848-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="7a848-130">WebSocket 要求を行うときに `Access-Control` ヘッダーに指定された制限を考慮する。</span><span class="sxs-lookup"><span data-stu-id="7a848-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="7a848-131">ただし、WebSocket 要求を発行するときにはブラウザーから `Origin` ヘッダーが送信されます。</span><span class="sxs-lookup"><span data-stu-id="7a848-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="7a848-132">予期した配信元からの WebSocket のみが許可されるように、アプリケーションでこれらのヘッダーが検証されるように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="7a848-133">ASP.NET Core 2.1 以降では、前`Configure` \*\* `UseSignalR`\*\* に配置したカスタムミドルウェアとの認証ミドルウェアを使用して、ヘッダーの検証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7a848-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="7a848-134">`Origin` ヘッダーは、クライアントによって制御され、`Referer` のように偽装することができます。</span><span class="sxs-lookup"><span data-stu-id="7a848-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="7a848-135">これらのヘッダーは、認証メカニズムとして使用**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="7a848-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="7a848-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="7a848-136">ConnectionId</span></span>

<span data-ttu-id="7a848-137">サーバー `ConnectionId`またはクライアントのバージョンが 2.2 SignalR以前 ASP.NET Core 場合、を公開すると、悪意のある偽装が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="7a848-138">サーバーとSignalRクライアントのバージョンが3.0 以降 ASP.NET Core 場合、では`ConnectionToken`なく、シークレット`ConnectionId`を保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="7a848-139">は`ConnectionToken` 、どの API でも意図的に公開されていません。</span><span class="sxs-lookup"><span data-stu-id="7a848-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="7a848-140">古いSignalRクライアントがサーバーに接続されていないことを確認するのは困難なSignalR場合があります。したがって、サーバー `ConnectionId`のバージョンが3.0 以降 ASP.NET Core 場合でも、が公開されないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="7a848-141">アクセストークンのログ記録</span><span class="sxs-lookup"><span data-stu-id="7a848-141">Access token logging</span></span>

<span data-ttu-id="7a848-142">Websocket またはサーバー送信イベントを使用する場合、ブラウザークライアントはクエリ文字列にアクセストークンを送信します。</span><span class="sxs-lookup"><span data-stu-id="7a848-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="7a848-143">一般に、クエリ文字列を使用してアクセストークンを受け取ること`Authorization`は、標準ヘッダーを使用するようにセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="7a848-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="7a848-144">クライアントとサーバー間のセキュリティで保護されたエンドツーエンド接続を確保するには、常に HTTPS を使用します。</span><span class="sxs-lookup"><span data-stu-id="7a848-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="7a848-145">多くの web サーバーでは、クエリ文字列を含め、各要求の URL がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="7a848-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="7a848-146">Url をログに記録すると、アクセストークンがログに記録される場合があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="7a848-147">では、各要求の URL が既定でログに記録されます。これには、クエリ文字列が含まれます。 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a848-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="7a848-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7a848-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="7a848-149">このデータをサーバーログに記録することに懸念がある場合は、 `Microsoft.AspNetCore.Hosting` logger を`Warning`レベル以上に構成することで、このログ記録を完全に無効に`Info`することができます (これらのメッセージはレベルで記述されます)。</span><span class="sxs-lookup"><span data-stu-id="7a848-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="7a848-150">詳細については、「[ログのフィルター処理](xref:fundamentals/logging/index#log-filtering)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a848-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="7a848-151">それでも特定の要求情報をログに記録する場合は、必要なデータをログに記録する[ミドルウェア](xref:fundamentals/middleware/write)を`access_token`作成し、クエリ文字列の値 (存在する場合) をフィルターで除外することができます。</span><span class="sxs-lookup"><span data-stu-id="7a848-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="7a848-152">例外</span><span class="sxs-lookup"><span data-stu-id="7a848-152">Exceptions</span></span>

<span data-ttu-id="7a848-153">例外メッセージは、通常、クライアントに公開されない機密データと見なされます。</span><span class="sxs-lookup"><span data-stu-id="7a848-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="7a848-154">既定ではSignalR 、はハブメソッドによってスローされた例外の詳細をクライアントに送信しません。</span><span class="sxs-lookup"><span data-stu-id="7a848-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="7a848-155">代わりに、エラーが発生したことを示す一般的なメッセージをクライアントが受信します。</span><span class="sxs-lookup"><span data-stu-id="7a848-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="7a848-156">クライアントへの例外メッセージの配信は、 [EnableDetailedErrors](xref:signalr/configuration#configure-server-options)を使用して (開発やテストなどで) オーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="7a848-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="7a848-157">例外メッセージは、実稼働アプリでクライアントに公開しないでください。</span><span class="sxs-lookup"><span data-stu-id="7a848-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="7a848-158">バッファー管理</span><span class="sxs-lookup"><span data-stu-id="7a848-158">Buffer management</span></span>

SignalR<span data-ttu-id="7a848-159">では、接続ごとのバッファーを使用して、受信メッセージと送信メッセージを管理します。</span><span class="sxs-lookup"><span data-stu-id="7a848-159"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="7a848-160">既定ではSignalR 、はこれらのバッファーを 32 KB に制限します。</span><span class="sxs-lookup"><span data-stu-id="7a848-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="7a848-161">クライアントまたはサーバーが送信できる最大メッセージサイズは 32 KB です。</span><span class="sxs-lookup"><span data-stu-id="7a848-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="7a848-162">メッセージの接続で消費される最大メモリは 32 KB です。</span><span class="sxs-lookup"><span data-stu-id="7a848-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="7a848-163">メッセージが常に 32 KB より小さい場合は、次の制限を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="7a848-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="7a848-164">クライアントが大きなメッセージを送信できないようにします。</span><span class="sxs-lookup"><span data-stu-id="7a848-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="7a848-165">サーバーは、メッセージを受け入れるために大きなバッファーを割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7a848-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="7a848-166">メッセージが 32 KB を超える場合は、制限を増やすことができます。</span><span class="sxs-lookup"><span data-stu-id="7a848-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="7a848-167">この制限を引き上げると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7a848-167">Increasing this limit means:</span></span>

* <span data-ttu-id="7a848-168">クライアントは、サーバーが大きなメモリバッファーを割り当てる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="7a848-169">大きなバッファーをサーバーに割り当てると、同時接続の数が減少する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="7a848-170">受信メッセージと送信メッセージには制限があり、どちらもで`MapHub`構成された[HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options)オブジェクトで構成できます。</span><span class="sxs-lookup"><span data-stu-id="7a848-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="7a848-171">`ApplicationMaxBufferSize`サーバーがバッファーするクライアントからの最大バイト数を表します。</span><span class="sxs-lookup"><span data-stu-id="7a848-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="7a848-172">クライアントがこの制限を超えるメッセージを送信しようとすると、接続が閉じられる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="7a848-173">`TransportMaxBufferSize`サーバーが送信できる最大バイト数を表します。</span><span class="sxs-lookup"><span data-stu-id="7a848-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="7a848-174">サーバーがこの制限を超えるメッセージ (ハブメソッドからの戻り値を含む) を送信しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="7a848-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="7a848-175">制限をに設定`0`すると、制限が無効になります。</span><span class="sxs-lookup"><span data-stu-id="7a848-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="7a848-176">制限を削除すると、クライアントは任意のサイズのメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="7a848-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="7a848-177">サイズの大きいメッセージを送信する悪意のあるクライアントは、余分なメモリが割り当てられる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7a848-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="7a848-178">メモリ使用量が多すぎると、同時接続の数が大幅に減少します。</span><span class="sxs-lookup"><span data-stu-id="7a848-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
