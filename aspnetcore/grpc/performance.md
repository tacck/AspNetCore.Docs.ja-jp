---
title: gRPC を使用したパフォーマンスのベスト プラクティス
author: jamesnk
description: 高パフォーマンスの gRPC サービスを構築するためのベスト プラクティスについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 622c6ba042c5832f99bba379fadd9aba7d7163f2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060405"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="66f39-103">gRPC を使用したパフォーマンスのベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="66f39-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="66f39-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="66f39-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="66f39-105">gRPC は、高パフォーマンス サービス向けに設計されています。</span><span class="sxs-lookup"><span data-stu-id="66f39-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="66f39-106">このドキュメントでは、gRPC から最大限のパフォーマンスを得る方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="66f39-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="66f39-107">gRPC チャネルを再利用する</span><span class="sxs-lookup"><span data-stu-id="66f39-107">Reuse gRPC channels</span></span>

<span data-ttu-id="66f39-108">gRPC の呼び出しを行う場合は、gRPC チャネルを再利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="66f39-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="66f39-109">チャネルを再利用すると、既存の HTTP/2 接続を介して、呼び出しを多重化できます。</span><span class="sxs-lookup"><span data-stu-id="66f39-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="66f39-110">gRPC 呼び出しごとに新しいチャネルが作成された場合、完了までにかかる時間が大幅に増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="66f39-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="66f39-111">呼び出しのたびに新しい HTTP/2 接続が作成されるため、クライアントとサーバーの間に次のような複数のネットワーク ラウンドトリップが必要になります。</span><span class="sxs-lookup"><span data-stu-id="66f39-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="66f39-112">ソケットを開く</span><span class="sxs-lookup"><span data-stu-id="66f39-112">Opening a socket</span></span>
2. <span data-ttu-id="66f39-113">TCP 接続を確立する</span><span class="sxs-lookup"><span data-stu-id="66f39-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="66f39-114">TLS ネゴシエーションを行う</span><span class="sxs-lookup"><span data-stu-id="66f39-114">Negotiating TLS</span></span>
4. <span data-ttu-id="66f39-115">HTTP/2 接続を開始する</span><span class="sxs-lookup"><span data-stu-id="66f39-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="66f39-116">gRPC 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="66f39-116">Making the gRPC call</span></span>

<span data-ttu-id="66f39-117">チャネルは、gRPC 呼び出しの間で安全に共有し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="66f39-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="66f39-118">gRPC クライアントはチャネルを使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="66f39-119">gRPC クライアントは軽量なオブジェクトであり、キャッシュしたり再利用したりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="66f39-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="66f39-120">異なる種類のクライアントを含め、1 つチャネルから複数の gRPC クライアントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="66f39-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="66f39-121">チャネルおよびそのチャネルから作成されたクライアントは、複数のスレッドで安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="66f39-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="66f39-122">チャネルから作成されたクライアントは、複数の同時呼び出しを行えます。</span><span class="sxs-lookup"><span data-stu-id="66f39-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="66f39-123">gRPC クライアント ファクトリには、チャネルを構成するための一元的な方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="66f39-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="66f39-124">基になるチャネルが自動的に再利用されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="66f39-125">詳細については、「<xref:grpc/clientfactory>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66f39-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="66f39-126">接続の同時実行</span><span class="sxs-lookup"><span data-stu-id="66f39-126">Connection concurrency</span></span>

<span data-ttu-id="66f39-127">HTTP/2 接続では、通常、1 つの接続で[同時に実行できるストリームの最大数 (アクティブな HTTP 要求数)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) に制限があります。</span><span class="sxs-lookup"><span data-stu-id="66f39-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="66f39-128">既定では、ほとんどのサーバーの同時に実行できるストリームの制限数は 100 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="66f39-129">gRPC チャネルは 1 つの HTTP/2 接続を使用し、その接続で同時呼び出しが多重化されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="66f39-130">アクティブな呼び出しの数が接続ストリームの制限に達すると、追加の呼び出しがクライアントのキューに入れられます。</span><span class="sxs-lookup"><span data-stu-id="66f39-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="66f39-131">キューに入れられた呼び出しは、アクティブな呼び出しが完了するのを待ってから送信されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="66f39-132">高負荷のアプリケーションや、長時間実行されるストリーミング gRPC 呼び出しでは、この制限により、呼び出しキューが原因でパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="66f39-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="66f39-133">.NET 5 により、`SocketsHttpHandler.EnableMultipleHttp2Connections` プロパティが導入されています。</span><span class="sxs-lookup"><span data-stu-id="66f39-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="66f39-134">`true` に設定すると、同時実行ストリームの制限に達したときに、チャネルによって追加の HTTP/2 接続が作成されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="66f39-135">`GrpcChannel` が作成されると、その内部の `SocketsHttpHandler` が、追加の HTTP/2 接続を作成するように自動的に構成されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="66f39-136">アプリによって独自のハンドラーが構成される場合は、`EnableMultipleHttp2Connections` を `true` に設定することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="66f39-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="66f39-137">.NET Core 3.1 アプリには、2 つの回避策があります。</span><span class="sxs-lookup"><span data-stu-id="66f39-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="66f39-138">高負荷のアプリの領域に対して、別々の gRPC チャネルを作成します。</span><span class="sxs-lookup"><span data-stu-id="66f39-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="66f39-139">たとえば、`Logger` gRPC サービスに高い負荷がかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="66f39-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="66f39-140">別個のチャネルを使用して、このアプリに `LoggerClient` を作成します。</span><span class="sxs-lookup"><span data-stu-id="66f39-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="66f39-141">gRPC チャネルのプールを使用します。たとえば、gRPC チャネルの一覧を作成します。</span><span class="sxs-lookup"><span data-stu-id="66f39-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="66f39-142">`Random` は、gRPC チャネルが必要になるたびに一覧からチャネルを選択するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="66f39-143">`Random` を使用すると、複数の接続で呼び出しがランダムに分散されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="66f39-144">この問題を解決するもう 1 つの方法は、サーバーの最大同時実行ストリーム制限を増やすことです。</span><span class="sxs-lookup"><span data-stu-id="66f39-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="66f39-145">Kestrel では、これは <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> で構成されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="66f39-146">最大同時実行ストリーム制限を増やすことはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="66f39-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="66f39-147">1 つの HTTP/2 接続でストリームが多すぎると、パフォーマンスの新たな問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="66f39-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="66f39-148">ストリーム間のスレッド競合により、接続への書き込みが試行されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="66f39-149">接続パケットが失われると、TCP 層ですべての呼び出しがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="66f39-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="66f39-150">負荷分散</span><span class="sxs-lookup"><span data-stu-id="66f39-150">Load balancing</span></span>

<span data-ttu-id="66f39-151">gRPC では、一部のロード バランサーが効率よく動作しません。</span><span class="sxs-lookup"><span data-stu-id="66f39-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="66f39-152">L4 (トランスポート) ロードバランサーは、TCP 接続をエンドポイント間で分散することで、接続レベルで動作します。</span><span class="sxs-lookup"><span data-stu-id="66f39-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="66f39-153">この方法は、HTTP/1.1 を使用して行われた API 呼び出しを負荷分散する場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="66f39-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="66f39-154">HTTP/1.1 で行われる同時呼び出しは異なる接続で送信されるため、エンドポイント間で負荷を分散することができます。</span><span class="sxs-lookup"><span data-stu-id="66f39-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="66f39-155">L4 ロード バランサーは接続レベルで動作するため、gRPC ではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="66f39-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="66f39-156">gRPC は HTTP/2 を使用します。これにより、1 つの TCP 接続での複数の呼び出しが多重化されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="66f39-157">その接続でのすべての gRPC 呼び出しが、1 つのエンドポイントに送られます。</span><span class="sxs-lookup"><span data-stu-id="66f39-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="66f39-158">gRPC を効果的に負荷分散するには、次の 2 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="66f39-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="66f39-159">クライアント側の負荷分散</span><span class="sxs-lookup"><span data-stu-id="66f39-159">Client-side load balancing</span></span>
* <span data-ttu-id="66f39-160">L7 (アプリケーション) のプロキシ負荷分散</span><span class="sxs-lookup"><span data-stu-id="66f39-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="66f39-161">gRPC 呼び出しのみが、エンドポイント間で負荷分散できます。</span><span class="sxs-lookup"><span data-stu-id="66f39-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="66f39-162">ストリーミング gRPC 呼び出しが確立されると、ストリームを介して送信されるすべてのメッセージが 1 つのエンドポイントに送られます。</span><span class="sxs-lookup"><span data-stu-id="66f39-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="66f39-163">クライアント側の負荷分散</span><span class="sxs-lookup"><span data-stu-id="66f39-163">Client-side load balancing</span></span>

<span data-ttu-id="66f39-164">クライアント側の負荷分散では、クライアントがエンドポイントを認識します。</span><span class="sxs-lookup"><span data-stu-id="66f39-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="66f39-165">各 gRPC 呼び出しで、呼び出しの送信先となる異なるエンドポイントが選択されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="66f39-166">クライアント側の負荷分散は、待機時間が重要な場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="66f39-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="66f39-167">クライアントとサービスの間にプロキシが存在しないため、呼び出しはサービスに直接送信されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="66f39-168">クライアント側の負荷分散の欠点は、各クライアントで使用可能なエンドポイントを追跡する必要があることです。</span><span class="sxs-lookup"><span data-stu-id="66f39-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="66f39-169">ルックアサイド クライアントの負荷分散は、負荷分散の状態を中央の場所に格納する手法です。</span><span class="sxs-lookup"><span data-stu-id="66f39-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="66f39-170">クライアントは、負荷分散の決定を行うときに使用する情報を中央の場所に定期的にクエリします。</span><span class="sxs-lookup"><span data-stu-id="66f39-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="66f39-171">`Grpc.Net.Client` は現在、クライアント側の負荷分散をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="66f39-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="66f39-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) は .NET でクライアント側の負荷分散が必要な場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="66f39-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="66f39-173">プロキシの負荷分散</span><span class="sxs-lookup"><span data-stu-id="66f39-173">Proxy load balancing</span></span>

<span data-ttu-id="66f39-174">L7 (アプリケーション) プロキシは、L4 (トランスポート) プロキシよりも高いレベルで動作します。</span><span class="sxs-lookup"><span data-stu-id="66f39-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="66f39-175">L7 プロキシは HTTP/2 を認識し、複数のエンドポイントにわたる 1 つの HTTP/2 接続で、gRPC 呼び出しをプロキシに多重化することができます。</span><span class="sxs-lookup"><span data-stu-id="66f39-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="66f39-176">プロキシの使用は、クライアント側の負荷分散よりも簡単ですが、gRPC 呼び出しに待機時間を追加することができます。</span><span class="sxs-lookup"><span data-stu-id="66f39-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="66f39-177">使用できる L7 プロキシは多数あります。</span><span class="sxs-lookup"><span data-stu-id="66f39-177">There are many L7 proxies available.</span></span> <span data-ttu-id="66f39-178">次のようなオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="66f39-178">Some options are:</span></span>

* <span data-ttu-id="66f39-179">[Envoy](https://www.envoyproxy.io/) - 広く普及しているオープン ソース プロキシ。</span><span class="sxs-lookup"><span data-stu-id="66f39-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="66f39-180">[Linkerd](https://linkerd.io/) - Kubernetes のサービス メッシュ。</span><span class="sxs-lookup"><span data-stu-id="66f39-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="66f39-181">[YARP: リバース プロキシ](https://microsoft.github.io/reverse-proxy/) - .NET で記述されたプレビューのオープン ソース プロキシ。</span><span class="sxs-lookup"><span data-stu-id="66f39-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="66f39-182">プロセス間通信</span><span class="sxs-lookup"><span data-stu-id="66f39-182">Inter-process communication</span></span>

<span data-ttu-id="66f39-183">クライアントとサービス間の gRPC 呼び出しは、通常、TCP ソケットを介して送信されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-183">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="66f39-184">TCP はネットワーク経由の通信に適していますが、クライアントとサービスが同じコンピューター上にある場合は、[プロセス間通信 (IPC)](https://wikipedia.org/wiki/Inter-process_communication) の方が効率的です。</span><span class="sxs-lookup"><span data-stu-id="66f39-184">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="66f39-185">同じマシン上のプロセス間の gRPC 呼び出しには、UNIX ドメイン ソケットや名前付きパイプなどのトランスポートを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="66f39-185">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="66f39-186">詳細については、「<xref:grpc/interprocess>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66f39-186">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="66f39-187">キープ アライブ ping</span><span class="sxs-lookup"><span data-stu-id="66f39-187">Keep alive pings</span></span>

<span data-ttu-id="66f39-188">キープ アライブ ping を使用して、非アクティブな状態の間 HTTP/2 接続を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="66f39-188">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="66f39-189">アプリでアクティビティが再開されるときに既存の HTTP/2 接続が準備できていると、接続の再確立による遅延が発生することなく、初期の gRPC 呼び出しをすばやく行うことができます。</span><span class="sxs-lookup"><span data-stu-id="66f39-189">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="66f39-190">キープ アライブ ping は <xref:System.Net.Http.SocketsHttpHandler> に構成されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-190">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="66f39-191">上記のコードにより、非アクティブな状態の間 60 秒ごとにキープ アライブ ping をサーバーに送信するチャネルが構成されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-191">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="66f39-192">ping により、サーバーと使用中のすべてのプロキシが、非アクティブが理由で接続が閉じられることがないように保証されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-192">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="66f39-193">ストリーム</span><span class="sxs-lookup"><span data-stu-id="66f39-193">Streaming</span></span>

<span data-ttu-id="66f39-194">gRPC 双方向ストリーミングを使用して、高パフォーマンスのシナリオで単項 gRPC 呼び出しを置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="66f39-194">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="66f39-195">双方向ストリームが開始されると、メッセージのやり取りが、複数の単項 gRPC 呼び出しでメッセージを送信するよりも高速になります。</span><span class="sxs-lookup"><span data-stu-id="66f39-195">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="66f39-196">ストリーム メッセージは、既存の HTTP/2 要求にデータとして送信され、各単項呼び出しに対して新しい HTTP/2 要求を作成するオーバーヘッドがなくなります。</span><span class="sxs-lookup"><span data-stu-id="66f39-196">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="66f39-197">サービスの例:</span><span class="sxs-lookup"><span data-stu-id="66f39-197">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="66f39-198">クライアントの例:</span><span class="sxs-lookup"><span data-stu-id="66f39-198">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="66f39-199">パフォーマンス上の理由で単項呼び出しを双方向ストリーミングに置き換えることは高度な手法であり、多くの状況では適切ではありません。</span><span class="sxs-lookup"><span data-stu-id="66f39-199">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="66f39-200">ストリーミング呼び出しの使用は、次の場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="66f39-200">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="66f39-201">高スループットまたは短い待機時間が必要とされている。</span><span class="sxs-lookup"><span data-stu-id="66f39-201">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="66f39-202">gRPC と HTTP/2 が、パフォーマンスのボトルネックとして特定された。</span><span class="sxs-lookup"><span data-stu-id="66f39-202">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="66f39-203">クライアントのワーカーが、gRPC サービスを使用して通常のメッセージを送受信している。</span><span class="sxs-lookup"><span data-stu-id="66f39-203">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="66f39-204">単項ではなく、ストリーミング呼び出しを使用する場合は、さらに複雑になり、制限事項が追加されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="66f39-204">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="66f39-205">ストリームは、サービスまたは接続エラーによって中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="66f39-205">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="66f39-206">エラーが発生した場合にストリームを再開するためのロジックが必要です。</span><span class="sxs-lookup"><span data-stu-id="66f39-206">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="66f39-207">`RequestStream.WriteAsync` は、マルチスレッドでは安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="66f39-207">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="66f39-208">一度に 1 つのストリームに書き込むことができるメッセージは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="66f39-208">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="66f39-209">1 つのストリームで複数のスレッドからメッセージを送信するには、メッセージをマーシャリングするための <xref:System.Threading.Channels.Channel%601> のようなプロデューサーまたはコンシューマー キューが必要です。</span><span class="sxs-lookup"><span data-stu-id="66f39-209">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="66f39-210">gRPC ストリーミング方式は、1 種類のメッセージの受信と 1 種類のメッセージの送信に制限されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-210">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="66f39-211">たとえば、`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` により、`RequestMessage` が受信され、`ResponseMessage` が送信されます。</span><span class="sxs-lookup"><span data-stu-id="66f39-211">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="66f39-212">`Any` と `oneof` を使用した不明なメッセージまたは条件付きメッセージに対する Protobuf のサポートにより、この制限を回避できます。</span><span class="sxs-lookup"><span data-stu-id="66f39-212">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
