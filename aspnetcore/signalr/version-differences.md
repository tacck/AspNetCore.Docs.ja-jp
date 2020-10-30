---
title: ':::no-loc(SignalR):::と ASP.NET Core の違い:::no-loc(SignalR):::'
author: bradygaster
description: ':::no-loc(SignalR):::と ASP.NET Core の違い:::no-loc(SignalR):::'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/version-differences
ms.openlocfilehash: c4c0ff83cb789e9aa35085496daa461404615726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061211"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="85b50-103">ASP.NET :::no-loc(SignalR)::: と ASP.NET Core の違い :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="85b50-103">Differences between ASP.NET :::no-loc(SignalR)::: and ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="85b50-104">ASP.NET Core :::no-loc(SignalR)::: は、ASP.NET のクライアントまたはサーバーと互換性がありません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="85b50-104">ASP.NET Core :::no-loc(SignalR)::: isn't compatible with clients or servers for ASP.NET :::no-loc(SignalR):::.</span></span> <span data-ttu-id="85b50-105">この記事では、ASP.NET Core で削除または変更された機能について詳しく説明 :::no-loc(SignalR)::: します。</span><span class="sxs-lookup"><span data-stu-id="85b50-105">This article details features which have been removed or changed in ASP.NET Core :::no-loc(SignalR):::.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="85b50-106">バージョンを識別する方法 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="85b50-106">How to identify the :::no-loc(SignalR)::: version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="85b50-107">ASP.NET :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="85b50-107">ASP.NET :::no-loc(SignalR):::</span></span> | <span data-ttu-id="85b50-108">ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="85b50-108">ASP.NET Core :::no-loc(SignalR):::</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="85b50-109">**サーバー NuGet パッケージ**</span><span class="sxs-lookup"><span data-stu-id="85b50-109">**Server NuGet package**</span></span> | <span data-ttu-id="85b50-110">[Microsoft AspNet。:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="85b50-110">[Microsoft.AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span></span> | <span data-ttu-id="85b50-111">なし。</span><span class="sxs-lookup"><span data-stu-id="85b50-111">None.</span></span> <span data-ttu-id="85b50-112">[AspNetCore](xref:fundamentals/metapackage-app)共有フレームワークに含まれています。</span><span class="sxs-lookup"><span data-stu-id="85b50-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="85b50-113">**クライアント NuGet パッケージ**</span><span class="sxs-lookup"><span data-stu-id="85b50-113">**Client NuGet packages**</span></span> | <span data-ttu-id="85b50-114">[Microsoft :::no-loc(SignalR)::: . AspNet.Client](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="85b50-114">[Microsoft.AspNet.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span></span><br><span data-ttu-id="85b50-115">[Microsoft :::no-loc(SignalR)::: . AspNet.NODE.JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span><span class="sxs-lookup"><span data-stu-id="85b50-115">[Microsoft.AspNet.:::no-loc(SignalR):::.JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span></span> | <span data-ttu-id="85b50-116">[AspNetCore :::no-loc(SignalR)::: .Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="85b50-116">[Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span></span> |
| <span data-ttu-id="85b50-117">**JavaScript クライアント npm パッケージ**</span><span class="sxs-lookup"><span data-stu-id="85b50-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="85b50-118">signalr</span><span class="sxs-lookup"><span data-stu-id="85b50-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="85b50-119">**Java クライアント**</span><span class="sxs-lookup"><span data-stu-id="85b50-119">**Java client**</span></span> | <span data-ttu-id="85b50-120">[GitHub リポジトリ](https://github.com/:::no-loc(SignalR):::/java-client) (非推奨)</span><span class="sxs-lookup"><span data-stu-id="85b50-120">[GitHub Repository](https://github.com/:::no-loc(SignalR):::/java-client) (deprecated)</span></span>  | <span data-ttu-id="85b50-121">Maven パッケージ [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="85b50-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="85b50-122">**サーバーアプリの種類**</span><span class="sxs-lookup"><span data-stu-id="85b50-122">**Server app type**</span></span> | <span data-ttu-id="85b50-123">ASP.NET (System.web) または OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="85b50-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="85b50-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85b50-124">ASP.NET Core</span></span> |
| <span data-ttu-id="85b50-125">**サポートされているサーバープラットフォーム**</span><span class="sxs-lookup"><span data-stu-id="85b50-125">**Supported server platforms**</span></span> | <span data-ttu-id="85b50-126">.NET Framework 4.5 以降</span><span class="sxs-lookup"><span data-stu-id="85b50-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="85b50-127">.NET Core 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="85b50-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="85b50-128">ASP.NET :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="85b50-128">ASP.NET :::no-loc(SignalR):::</span></span> | <span data-ttu-id="85b50-129">ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="85b50-129">ASP.NET Core :::no-loc(SignalR):::</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="85b50-130">**サーバー NuGet パッケージ**</span><span class="sxs-lookup"><span data-stu-id="85b50-130">**Server NuGet package**</span></span> | <span data-ttu-id="85b50-131">[Microsoft AspNet。:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="85b50-131">[Microsoft.AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span></span> | <span data-ttu-id="85b50-132">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="85b50-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="85b50-133">[AspNetCore。:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="85b50-133">[Microsoft.AspNetCore.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::/)</span></span> <span data-ttu-id="85b50-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="85b50-134">(.NET Framework)</span></span> |
| <span data-ttu-id="85b50-135">**クライアント NuGet パッケージ**</span><span class="sxs-lookup"><span data-stu-id="85b50-135">**Client NuGet packages**</span></span> | <span data-ttu-id="85b50-136">[Microsoft :::no-loc(SignalR)::: . AspNet.Client](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="85b50-136">[Microsoft.AspNet.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span></span><br><span data-ttu-id="85b50-137">[Microsoft :::no-loc(SignalR)::: . AspNet.NODE.JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span><span class="sxs-lookup"><span data-stu-id="85b50-137">[Microsoft.AspNet.:::no-loc(SignalR):::.JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span></span> | <span data-ttu-id="85b50-138">[AspNetCore :::no-loc(SignalR)::: .Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="85b50-138">[Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span></span> |
| <span data-ttu-id="85b50-139">**JavaScript クライアント npm パッケージ**</span><span class="sxs-lookup"><span data-stu-id="85b50-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="85b50-140">signalr</span><span class="sxs-lookup"><span data-stu-id="85b50-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="85b50-141">**Java クライアント**</span><span class="sxs-lookup"><span data-stu-id="85b50-141">**Java client**</span></span> | <span data-ttu-id="85b50-142">[GitHub リポジトリ](https://github.com/:::no-loc(SignalR):::/java-client) (非推奨)</span><span class="sxs-lookup"><span data-stu-id="85b50-142">[GitHub Repository](https://github.com/:::no-loc(SignalR):::/java-client) (deprecated)</span></span>  | <span data-ttu-id="85b50-143">Maven パッケージ [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="85b50-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="85b50-144">**サーバーアプリの種類**</span><span class="sxs-lookup"><span data-stu-id="85b50-144">**Server app type**</span></span> | <span data-ttu-id="85b50-145">ASP.NET (System.web) または OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="85b50-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="85b50-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85b50-146">ASP.NET Core</span></span> |
| <span data-ttu-id="85b50-147">**サポートされているサーバープラットフォーム**</span><span class="sxs-lookup"><span data-stu-id="85b50-147">**Supported server platforms**</span></span> | <span data-ttu-id="85b50-148">.NET Framework 4.5 以降</span><span class="sxs-lookup"><span data-stu-id="85b50-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="85b50-149">.NET Framework 4.6.1 以降</span><span class="sxs-lookup"><span data-stu-id="85b50-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="85b50-150">.NET Core 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="85b50-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="85b50-151">機能の違い</span><span class="sxs-lookup"><span data-stu-id="85b50-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="85b50-152">自動再接続</span><span class="sxs-lookup"><span data-stu-id="85b50-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85b50-153">ASP.NET の場合 :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="85b50-153">In ASP.NET :::no-loc(SignalR)::::</span></span>

* <span data-ttu-id="85b50-154">既定では、は、 :::no-loc(SignalR)::: 接続が切断された場合にサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="85b50-154">By default, :::no-loc(SignalR)::: attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="85b50-155">ASP.NET Core :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="85b50-155">In ASP.NET Core :::no-loc(SignalR)::::</span></span>

* <span data-ttu-id="85b50-156">自動再接続は、 [.net クライアント](xref:signalr/dotnet-client#automatically-reconnect) と [JavaScript クライアント](xref:signalr/javascript-client#automatically-reconnect)の両方にオプトインされています。</span><span class="sxs-lookup"><span data-stu-id="85b50-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="85b50-157">ASP.NET Core 3.0 より前では、は自動再接続をサポートして :::no-loc(SignalR)::: いません。</span><span class="sxs-lookup"><span data-stu-id="85b50-157">Prior to ASP.NET Core 3.0, :::no-loc(SignalR)::: doesn't support automatic reconnects.</span></span> <span data-ttu-id="85b50-158">クライアントが切断されている場合、ユーザーは新しい接続を明示的に開始して再接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85b50-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="85b50-159">ASP.NET では :::no-loc(SignalR)::: 、 :::no-loc(SignalR)::: 接続が切断された場合、はサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="85b50-159">In ASP.NET :::no-loc(SignalR):::, :::no-loc(SignalR)::: attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="85b50-160">プロトコルのサポート</span><span class="sxs-lookup"><span data-stu-id="85b50-160">Protocol support</span></span>

<span data-ttu-id="85b50-161">ASP.NET Core :::no-loc(SignalR)::: は、 [messagepack](xref:signalr/messagepackhubprotocol)に基づく新しいバイナリプロトコルだけでなく、JSON もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="85b50-161">ASP.NET Core :::no-loc(SignalR)::: supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="85b50-162">さらに、カスタムプロトコルを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="85b50-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="85b50-163">トランスポート</span><span class="sxs-lookup"><span data-stu-id="85b50-163">Transports</span></span>

<span data-ttu-id="85b50-164">永続的フレーム転送は ASP.NET Core ではサポートされていません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="85b50-164">The Forever Frame transport isn't supported in ASP.NET Core :::no-loc(SignalR):::.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="85b50-165">サーバーの相違点</span><span class="sxs-lookup"><span data-stu-id="85b50-165">Differences on the server</span></span>

<span data-ttu-id="85b50-166">ASP.NET Core :::no-loc(SignalR)::: サーバー側ライブラリは、 [AspNetCore](xref:fundamentals/metapackage-app)に含まれています。これは、と MVC プロジェクトの両方の **ASP.NET Core Web アプリケーション** テンプレートで使用されます。 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="85b50-166">The ASP.NET Core :::no-loc(SignalR)::: server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both :::no-loc(Razor)::: and MVC projects.</span></span>

<span data-ttu-id="85b50-167">ASP.NET Core :::no-loc(SignalR)::: は ASP.NET Core ミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="85b50-167">ASP.NET Core :::no-loc(SignalR)::: is an ASP.NET Core middleware.</span></span> <span data-ttu-id="85b50-168">のを呼び出すことによって構成する必要があり <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.Add:::no-loc(SignalR):::%2A> `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="85b50-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.Add:::no-loc(SignalR):::%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85b50-169">ルーティングを構成するには、メソッド内のメソッド呼び出し内のハブにルートをマップし <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="85b50-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="85b50-170">ルーティングを構成するには、メソッド内のメソッド呼び出し内のハブにルートをマップし <xref:Microsoft.AspNetCore.Builder.:::no-loc(SignalR):::AppBuilderExtensions.Use:::no-loc(SignalR):::%2A> `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="85b50-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.:::no-loc(SignalR):::AppBuilderExtensions.Use:::no-loc(SignalR):::%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.Use:::no-loc(SignalR):::(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="85b50-171">固定セッション</span><span class="sxs-lookup"><span data-stu-id="85b50-171">Sticky sessions</span></span>

<span data-ttu-id="85b50-172">ASP.NET のスケールアウトモデルを :::no-loc(SignalR)::: 使用すると、クライアントは再接続し、ファーム内の任意のサーバーにメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="85b50-172">The scaleout model for ASP.NET :::no-loc(SignalR)::: allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="85b50-173">ASP.NET Core では、 :::no-loc(SignalR)::: クライアントは接続中に同じサーバーと通信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85b50-173">In ASP.NET Core :::no-loc(SignalR):::, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="85b50-174">Redis を使用したスケールアウトの場合、これは固定セッションが必要であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="85b50-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="85b50-175">[Azure :::no-loc(SignalR)::: サービス](/azure/azure-signalr/)を使用したスケールアウトでは、サービスがクライアントへの接続を処理するため、固定セッションは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="85b50-175">For scaleout using [Azure :::no-loc(SignalR)::: Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="85b50-176">接続ごとに1つのハブ</span><span class="sxs-lookup"><span data-stu-id="85b50-176">Single hub per connection</span></span>

<span data-ttu-id="85b50-177">ASP.NET Core では :::no-loc(SignalR)::: 、接続モデルが単純化されています。</span><span class="sxs-lookup"><span data-stu-id="85b50-177">In ASP.NET Core :::no-loc(SignalR):::, the connection model has been simplified.</span></span> <span data-ttu-id="85b50-178">複数のハブへのアクセスを共有するために使用される単一の接続ではなく、1つのハブに直接接続されます。</span><span class="sxs-lookup"><span data-stu-id="85b50-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="85b50-179">ストリーミング</span><span class="sxs-lookup"><span data-stu-id="85b50-179">Streaming</span></span>

<span data-ttu-id="85b50-180">ASP.NET Core は、 :::no-loc(SignalR)::: ハブからクライアントへの [データのストリーミング](xref:signalr/streaming) をサポートするようになりました。</span><span class="sxs-lookup"><span data-stu-id="85b50-180">ASP.NET Core :::no-loc(SignalR)::: now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="85b50-181">State</span><span class="sxs-lookup"><span data-stu-id="85b50-181">State</span></span>

<span data-ttu-id="85b50-182">クライアントとハブ (多くの場合はと呼ばれます) の間で任意の状態を渡す機能は `HubState` 削除されており、進行状況メッセージもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="85b50-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="85b50-183">現時点では、対応するハブプロキシはありません。</span><span class="sxs-lookup"><span data-stu-id="85b50-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="85b50-184">PersistentConnection の削除</span><span class="sxs-lookup"><span data-stu-id="85b50-184">PersistentConnection removal</span></span>

<span data-ttu-id="85b50-185">ASP.NET Core では :::no-loc(SignalR)::: 、 [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) クラスは削除されています。</span><span class="sxs-lookup"><span data-stu-id="85b50-185">In ASP.NET Core :::no-loc(SignalR):::, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="85b50-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="85b50-186">GlobalHost</span></span>

<span data-ttu-id="85b50-187">ASP.NET Core には、フレームワークに依存関係の挿入 (DI) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="85b50-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="85b50-188">サービスは DI を使用して [HubContext](xref:signalr/hubcontext)にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="85b50-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="85b50-189">`GlobalHost`を取得するために ASP.NET で使用されるオブジェクトが :::no-loc(SignalR)::: `HubContext` ASP.NET Core に存在しません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="85b50-189">The `GlobalHost` object that is used in ASP.NET :::no-loc(SignalR)::: to get a `HubContext` doesn't exist in ASP.NET Core :::no-loc(SignalR):::.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="85b50-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="85b50-190">HubPipeline</span></span>

<span data-ttu-id="85b50-191">ASP.NET Core :::no-loc(SignalR)::: はモジュールをサポートしていません `HubPipeline` 。</span><span class="sxs-lookup"><span data-stu-id="85b50-191">ASP.NET Core :::no-loc(SignalR)::: doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="85b50-192">クライアントの相違点</span><span class="sxs-lookup"><span data-stu-id="85b50-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="85b50-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="85b50-193">TypeScript</span></span>

<span data-ttu-id="85b50-194">ASP.NET Core :::no-loc(SignalR)::: クライアントは [TypeScript](https://www.typescriptlang.org/)で記述されています。</span><span class="sxs-lookup"><span data-stu-id="85b50-194">The ASP.NET Core :::no-loc(SignalR)::: client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="85b50-195">Javascript [クライアント](xref:signalr/javascript-client)を使用する場合は、javascript または TypeScript で記述できます。</span><span class="sxs-lookup"><span data-stu-id="85b50-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="85b50-196">JavaScript クライアントは npm でホストされます。</span><span class="sxs-lookup"><span data-stu-id="85b50-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85b50-197">ASP.NET のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを使用して取得されました。</span><span class="sxs-lookup"><span data-stu-id="85b50-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="85b50-198">ASP.NET Core のバージョンでは、 [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm パッケージに JavaScript ライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="85b50-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="85b50-199">このパッケージは、 **ASP.NET Core Web アプリケーション** テンプレートには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="85b50-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="85b50-200">Npm パッケージを取得してインストールするには、npm を使用し `@microsoft/signalr` ます。</span><span class="sxs-lookup"><span data-stu-id="85b50-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="85b50-201">ASP.NET のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを使用して取得されました。</span><span class="sxs-lookup"><span data-stu-id="85b50-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="85b50-202">ASP.NET Core のバージョンでは、 [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm パッケージに JavaScript ライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="85b50-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="85b50-203">このパッケージは、 **ASP.NET Core Web アプリケーション** テンプレートには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="85b50-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="85b50-204">Npm パッケージを取得してインストールするには、npm を使用し `@aspnet/signalr` ます。</span><span class="sxs-lookup"><span data-stu-id="85b50-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="85b50-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="85b50-205">jQuery</span></span>

<span data-ttu-id="85b50-206">JQuery への依存関係は削除されましたが、プロジェクトは引き続き jQuery を使用できます。</span><span class="sxs-lookup"><span data-stu-id="85b50-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="85b50-207">Internet Explorer のサポート</span><span class="sxs-lookup"><span data-stu-id="85b50-207">Internet Explorer support</span></span>

<span data-ttu-id="85b50-208">ASP.NET Core で :::no-loc(SignalR)::: は、Microsoft Internet explorer 11 以降がサポートされていますが、ASP.NET で :::no-loc(SignalR)::: は Microsoft internet explorer 8 以降がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="85b50-208">ASP.NET Core :::no-loc(SignalR)::: supports Microsoft Internet Explorer 11 or later, whereas ASP.NET :::no-loc(SignalR)::: supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="85b50-209">ブラウザーサポートの詳細については、「 [サポートされているプラットフォーム](xref:signalr/supported-platforms#javascript-client)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85b50-209">More info on browser support can be found at [supported platforms](xref:signalr/supported-platforms#javascript-client).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="85b50-210">JavaScript クライアントメソッドの構文</span><span class="sxs-lookup"><span data-stu-id="85b50-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85b50-211">JavaScript の構文は、ASP.NET バージョンのから変更されました :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="85b50-211">The JavaScript syntax has changed from the ASP.NET version of :::no-loc(SignalR):::.</span></span> <span data-ttu-id="85b50-212">オブジェクトを使用するのではなく `$connection` 、 [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API を使用して接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="85b50-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="85b50-213">[On](/javascript/api/@microsoft/signalr/HubConnection#on)メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="85b50-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="85b50-214">JavaScript の構文は、ASP.NET バージョンのから変更されました :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="85b50-214">The JavaScript syntax has changed from the ASP.NET version of :::no-loc(SignalR):::.</span></span> <span data-ttu-id="85b50-215">オブジェクトを使用するのではなく `$connection` 、 [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API を使用して接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="85b50-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="85b50-216">[On](/javascript/api/@aspnet/signalr/HubConnection#on)メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="85b50-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="85b50-217">クライアントメソッドを作成したら、ハブ接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="85b50-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="85b50-218">[Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)メソッドをチェーンして、エラーをログに記録または処理します。</span><span class="sxs-lookup"><span data-stu-id="85b50-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="85b50-219">ハブプロキシ</span><span class="sxs-lookup"><span data-stu-id="85b50-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85b50-220">ハブプロキシが自動的に生成されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="85b50-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="85b50-221">代わりに、メソッド名が文字列として [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API に渡されます。</span><span class="sxs-lookup"><span data-stu-id="85b50-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="85b50-222">ハブプロキシが自動的に生成されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="85b50-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="85b50-223">代わりに、メソッド名が文字列として [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API に渡されます。</span><span class="sxs-lookup"><span data-stu-id="85b50-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="85b50-224">.NET とその他のクライアント</span><span class="sxs-lookup"><span data-stu-id="85b50-224">.NET and other clients</span></span>

<span data-ttu-id="85b50-225">[AspNetCore. :::no-loc(SignalR)::: .クライアント](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client)NuGet パッケージには、ASP.NET Core 用の .net クライアントライブラリが含まれてい :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="85b50-225">The [Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) NuGet package contains the .NET client libraries for ASP.NET Core :::no-loc(SignalR):::.</span></span>

<span data-ttu-id="85b50-226"><xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>ハブへの接続のインスタンスを作成して構築するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="85b50-226">Use the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="85b50-227">スケールアウトの相違点</span><span class="sxs-lookup"><span data-stu-id="85b50-227">Scaleout differences</span></span>

<span data-ttu-id="85b50-228">ASP.NET は :::no-loc(SignalR)::: SQL Server と Redis をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="85b50-228">ASP.NET :::no-loc(SignalR)::: supports SQL Server and Redis.</span></span> <span data-ttu-id="85b50-229">ASP.NET Core :::no-loc(SignalR)::: では :::no-loc(SignalR)::: 、Azure サービスと Redis がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="85b50-229">ASP.NET Core :::no-loc(SignalR)::: supports Azure :::no-loc(SignalR)::: Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="85b50-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="85b50-230">ASP.NET</span></span>

* [<span data-ttu-id="85b50-231">:::no-loc(SignalR)::: Azure Service Bus によるスケールアウト</span><span class="sxs-lookup"><span data-stu-id="85b50-231">:::no-loc(SignalR)::: scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="85b50-232">:::no-loc(SignalR)::: Redis によるスケールアウト</span><span class="sxs-lookup"><span data-stu-id="85b50-232">:::no-loc(SignalR)::: scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="85b50-233">:::no-loc(SignalR)::: SQL Server によるスケールアウト</span><span class="sxs-lookup"><span data-stu-id="85b50-233">:::no-loc(SignalR)::: scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="85b50-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85b50-234">ASP.NET Core</span></span>

* [<span data-ttu-id="85b50-235">Azure :::no-loc(SignalR)::: サービス</span><span class="sxs-lookup"><span data-stu-id="85b50-235">Azure :::no-loc(SignalR)::: Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="85b50-236">Redis バックプレーン</span><span class="sxs-lookup"><span data-stu-id="85b50-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="85b50-237">その他の資料</span><span class="sxs-lookup"><span data-stu-id="85b50-237">Additional resources</span></span>

* [<span data-ttu-id="85b50-238">ハブ</span><span class="sxs-lookup"><span data-stu-id="85b50-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="85b50-239">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="85b50-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="85b50-240">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="85b50-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="85b50-241">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="85b50-241">Supported platforms</span></span>](xref:signalr/supported-platforms)
