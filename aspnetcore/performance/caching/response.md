---
title: ASP.NET Core での応答のキャッシュ
author: rick-anderson
description: 応答キャッシュを使用し、帯域幅要件を下げ、ASP.NET Core アプリのパフォーマンスを上げる方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
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
uid: performance/caching/response
ms.openlocfilehash: 9516410399ce69f1d69b09781b2530d052a11e7a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631876"
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="ffa11-103">ASP.NET Core での応答のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="ffa11-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="ffa11-104">By [John Luo](https://github.com/JunTaoLuo)、 [Rick Anderson](https://twitter.com/RickAndMSFT)、および [上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ffa11-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ffa11-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ffa11-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ffa11-106">応答キャッシュを使用すると、クライアントまたはプロキシが web サーバーに対して行う要求の数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-106">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="ffa11-107">応答キャッシュを使用すると、web サーバーが応答を生成するために実行する作業量も少なくなります。</span><span class="sxs-lookup"><span data-stu-id="ffa11-107">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="ffa11-108">応答のキャッシュは、クライアント、プロキシ、およびミドルウェアが応答をキャッシュする方法を指定するヘッダーによって制御されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-108">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="ffa11-109">[ResponseCache 属性](#responsecache-attribute)は、応答キャッシュヘッダーの設定に関与します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-109">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="ffa11-110">クライアントと中間プロキシは、 [HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234)での応答をキャッシュするためのヘッダーを優先する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffa11-110">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="ffa11-111">HTTP 1.1 キャッシュ仕様に従ったサーバー側キャッシュの場合は、 [応答キャッシュミドルウェア](xref:performance/caching/middleware)を使用します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-111">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="ffa11-112">ミドルウェアは、プロパティを使用して、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> サーバー側のキャッシュ動作に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-112">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="ffa11-113">HTTP ベースの応答のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="ffa11-113">HTTP-based response caching</span></span>

<span data-ttu-id="ffa11-114">[HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234)では、インターネットキャッシュの動作方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-114">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="ffa11-115">キャッシュに使用されるプライマリ HTTP ヘッダーは [cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)で、キャッシュ *ディレクティブ*を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-115">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="ffa11-116">ディレクティブは、要求に応じてキャッシュ動作を制御し、応答としてサーバーからクライアントへの応答を行います。</span><span class="sxs-lookup"><span data-stu-id="ffa11-116">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="ffa11-117">要求と応答はプロキシサーバーを経由して移動し、プロキシサーバーも HTTP 1.1 キャッシュ仕様に準拠している必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffa11-117">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="ffa11-118">共通の `Cache-Control` ディレクティブを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-118">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="ffa11-119">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="ffa11-119">Directive</span></span>                                                       | <span data-ttu-id="ffa11-120">アクション</span><span class="sxs-lookup"><span data-stu-id="ffa11-120">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="ffa11-121">public</span><span class="sxs-lookup"><span data-stu-id="ffa11-121">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="ffa11-122">キャッシュは応答を格納できます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-122">A cache may store the response.</span></span> |
| [<span data-ttu-id="ffa11-123">private</span><span class="sxs-lookup"><span data-stu-id="ffa11-123">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="ffa11-124">応答は、共有キャッシュによって格納されていない必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffa11-124">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="ffa11-125">プライベートキャッシュは、応答を格納して再利用できます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-125">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="ffa11-126">最長有効期間</span><span class="sxs-lookup"><span data-stu-id="ffa11-126">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="ffa11-127">クライアントは、指定された秒数よりも有効期間が長い応答を受け入れません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-127">The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="ffa11-128">例: `max-age=60` (60 秒)、 `max-age=2592000` (1 か月)</span><span class="sxs-lookup"><span data-stu-id="ffa11-128">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="ffa11-129">キャッシュなし</span><span class="sxs-lookup"><span data-stu-id="ffa11-129">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="ffa11-130">**要求時**: キャッシュは、要求を満たすために格納された応答を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-130">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="ffa11-131">配信元サーバーはクライアントの応答を再生成し、ミドルウェアはキャッシュに格納されている応答を更新します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-131">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="ffa11-132">**応答時**: 配信元サーバーで検証を行わずに、後続の要求に応答を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-132">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="ffa11-133">ストアなし</span><span class="sxs-lookup"><span data-stu-id="ffa11-133">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="ffa11-134">**要求時**: キャッシュは要求を格納できません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-134">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="ffa11-135">**応答**の場合: キャッシュは、応答の一部を格納することはできません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-135">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="ffa11-136">キャッシュでロールを果たすその他のキャッシュヘッダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-136">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="ffa11-137">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="ffa11-137">Header</span></span>                                                     | <span data-ttu-id="ffa11-138">機能</span><span class="sxs-lookup"><span data-stu-id="ffa11-138">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="ffa11-139">変更</span><span class="sxs-lookup"><span data-stu-id="ffa11-139">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="ffa11-140">配信元サーバーで応答が生成または正常に検証されてからの、秒単位の推定時間。</span><span class="sxs-lookup"><span data-stu-id="ffa11-140">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="ffa11-141">経過</span><span class="sxs-lookup"><span data-stu-id="ffa11-141">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="ffa11-142">応答が古くなったと見なされるまでの時間。</span><span class="sxs-lookup"><span data-stu-id="ffa11-142">The time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="ffa11-143">Unmanaged</span><span class="sxs-lookup"><span data-stu-id="ffa11-143">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="ffa11-144">動作を設定するために HTTP/1.0 キャッシュとの下位互換性を維持するために存在し `no-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-144">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="ffa11-145">`Cache-Control`ヘッダーが存在する場合、 `Pragma` ヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-145">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="ffa11-146">要因</span><span class="sxs-lookup"><span data-stu-id="ffa11-146">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="ffa11-147">キャッシュされた `Vary` 応答の元の要求と新しい要求の両方ですべてのヘッダーフィールドが一致する場合を除き、キャッシュされた応答を送信しないように指定します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-147">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="ffa11-148">HTTP ベースのキャッシュは、要求のキャッシュ制御ディレクティブを尊重します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-148">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="ffa11-149">[Cache-control ヘッダーの HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)では、 `Cache-Control` クライアントから送信された有効なヘッダーを優先するキャッシュが必要です。</span><span class="sxs-lookup"><span data-stu-id="ffa11-149">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="ffa11-150">クライアントは、ヘッダー値を使用して要求を行い、 `no-cache` すべての要求に対して新しい応答を強制的に生成するようにサーバーに要求できます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-150">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="ffa11-151">`Cache-Control`HTTP キャッシュの目的を検討する場合は、常にクライアント要求ヘッダーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ffa11-151">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="ffa11-152">公式仕様では、キャッシュは、クライアント、プロキシ、およびサーバーのネットワーク経由で要求を満たすことの待機時間とネットワークオーバーヘッドを削減することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="ffa11-152">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="ffa11-153">配信元サーバーの負荷を制御する方法であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-153">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="ffa11-154">ミドルウェアが公式のキャッシュ仕様に準拠しているため、 [応答キャッシュミドルウェア](xref:performance/caching/middleware) を使用する場合、開発者はこのキャッシュ動作を制御することはできません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-154">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="ffa11-155">[ミドルウェアの計画](https://github.com/dotnet/AspNetCore/issues/2612) された拡張機能は、キャッシュされ `Cache-Control` た応答を提供することを決定するときに、要求のヘッダーを無視するようにミドルウェアを構成する機会です。</span><span class="sxs-lookup"><span data-stu-id="ffa11-155">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="ffa11-156">計画された拡張機能により、サーバーの負荷をより適切に制御できるようになります。</span><span class="sxs-lookup"><span data-stu-id="ffa11-156">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="ffa11-157">ASP.NET Core のその他のキャッシュテクノロジ</span><span class="sxs-lookup"><span data-stu-id="ffa11-157">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="ffa11-158">メモリ内キャッシュ</span><span class="sxs-lookup"><span data-stu-id="ffa11-158">In-memory caching</span></span>

<span data-ttu-id="ffa11-159">インメモリキャッシュは、キャッシュされたデータを格納するためにサーバーメモリを使用します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-159">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="ffa11-160">この種のキャッシュは、1台のサーバーまたは *固定セッション*を使用している複数のサーバーに適しています。</span><span class="sxs-lookup"><span data-stu-id="ffa11-160">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="ffa11-161">固定セッションとは、クライアントからの要求が常に同じサーバーにルーティングされて処理されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-161">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="ffa11-162">詳細については、<xref:performance/caching/memory> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffa11-162">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="ffa11-163">分散キャッシュ</span><span class="sxs-lookup"><span data-stu-id="ffa11-163">Distributed Cache</span></span>

<span data-ttu-id="ffa11-164">アプリがクラウドまたはサーバーファームでホストされている場合は、分散キャッシュを使用してデータをメモリに格納します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-164">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="ffa11-165">キャッシュは、要求を処理するサーバー間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-165">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="ffa11-166">クライアントのキャッシュデータが使用可能な場合、クライアントは、グループ内の任意のサーバーによって処理される要求を送信できます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-166">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="ffa11-167">ASP.NET Core は、SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)、および [ncache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 分散キャッシュと連動します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-167">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="ffa11-168">詳細については、<xref:performance/caching/distributed> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffa11-168">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="ffa11-169">キャッシュ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="ffa11-169">Cache Tag Helper</span></span>

<span data-ttu-id="ffa11-170">キャッシュタグヘルパーを使用して、MVC ビューまたはページからコンテンツをキャッシュし Razor ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-170">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="ffa11-171">キャッシュタグヘルパーは、メモリ内キャッシュを使用してデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-171">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="ffa11-172">詳細については、<xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffa11-172">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="ffa11-173">分散キャッシュ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="ffa11-173">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="ffa11-174">分散 Razor キャッシュタグヘルパーを使用して、分散型クラウドまたは web ファームのシナリオで、MVC ビューまたはページからコンテンツをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="ffa11-174">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="ffa11-175">分散キャッシュタグヘルパーは、SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)、または [ncache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) を使用してデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-175">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="ffa11-176">詳細については、<xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffa11-176">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="ffa11-177">ResponseCache 属性</span><span class="sxs-lookup"><span data-stu-id="ffa11-177">ResponseCache attribute</span></span>

<span data-ttu-id="ffa11-178">は、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 応答キャッシュに適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-178">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="ffa11-179">認証されたクライアントの情報が含まれているコンテンツのキャッシュを無効にします。</span><span class="sxs-lookup"><span data-stu-id="ffa11-179">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="ffa11-180">キャッシュは、ユーザーの id またはユーザーがサインインしているかどうかによって変更されないコンテンツに対してのみ有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffa11-180">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="ffa11-181"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 指定されたクエリキーのリストの値によって、格納されている応答を変化させる。</span><span class="sxs-lookup"><span data-stu-id="ffa11-181"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="ffa11-182">1つの値を指定した場合 `*` 、ミドルウェアはすべての要求クエリ文字列パラメーターによって応答を変化させることができます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-182">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="ffa11-183">プロパティを設定するには、[応答キャッシュミドルウェア](xref:performance/caching/middleware)を有効にする必要があり <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-183">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="ffa11-184">それ以外の場合は、ランタイム例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-184">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="ffa11-185">プロパティに対応する HTTP ヘッダーがありません <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 。</span><span class="sxs-lookup"><span data-stu-id="ffa11-185">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="ffa11-186">プロパティは、応答キャッシュミドルウェアによって処理される HTTP 機能です。</span><span class="sxs-lookup"><span data-stu-id="ffa11-186">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="ffa11-187">ミドルウェアがキャッシュされた応答を提供するには、クエリ文字列とクエリ文字列の値が以前の要求と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffa11-187">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="ffa11-188">たとえば、次の表に示すような一連の要求と結果を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ffa11-188">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="ffa11-189">Request</span><span class="sxs-lookup"><span data-stu-id="ffa11-189">Request</span></span>                          | <span data-ttu-id="ffa11-190">結果</span><span class="sxs-lookup"><span data-stu-id="ffa11-190">Result</span></span>                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | <span data-ttu-id="ffa11-191">サーバーから返されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-191">Returned from the server.</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="ffa11-192">ミドルウェアから返されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-192">Returned from middleware.</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="ffa11-193">サーバーから返されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-193">Returned from the server.</span></span> |

<span data-ttu-id="ffa11-194">最初の要求はサーバーによって返され、ミドルウェアにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-194">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="ffa11-195">2番目の要求は、クエリ文字列が前の要求と一致するため、ミドルウェアによって返されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-195">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="ffa11-196">クエリ文字列値が以前の要求と一致しないので、3番目の要求がミドルウェアキャッシュにありません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-196">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="ffa11-197">は、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> を使用してを構成および作成するために使用され <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-197">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="ffa11-198">は、 `ResponseCacheFilter` 適切な HTTP ヘッダーと応答の機能を更新する作業を実行します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-198">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="ffa11-199">フィルター:</span><span class="sxs-lookup"><span data-stu-id="ffa11-199">The filter:</span></span>

* <span data-ttu-id="ffa11-200">、、およびの既存のヘッダーを削除 `Vary` `Cache-Control` `Pragma` します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-200">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="ffa11-201">で設定されたプロパティに基づいて、適切なヘッダーを書き込み <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-201">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="ffa11-202">が設定されている場合は、応答キャッシュ HTTP 機能を更新し <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-202">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="ffa11-203">要因</span><span class="sxs-lookup"><span data-stu-id="ffa11-203">Vary</span></span>

<span data-ttu-id="ffa11-204">このヘッダーは、プロパティが設定されている場合にのみ書き込まれ <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-204">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="ffa11-205">プロパティの値に設定されたプロパティ `Vary` 。</span><span class="sxs-lookup"><span data-stu-id="ffa11-205">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="ffa11-206">次の例では、プロパティを使用し <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-206">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="ffa11-207">サンプルアプリを使用して、ブラウザーのネットワークツールで応答ヘッダーを表示します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-207">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="ffa11-208">次の応答ヘッダーは、Cache1 ページの応答と共に送信されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-208">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="ffa11-209">NoStore と Location。なし</span><span class="sxs-lookup"><span data-stu-id="ffa11-209">NoStore and Location.None</span></span>

<span data-ttu-id="ffa11-210"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> その他のほとんどのプロパティをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ffa11-210"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="ffa11-211">このプロパティがに設定されている場合 `true` 、 `Cache-Control` ヘッダーはに設定され `no-store` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-211">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="ffa11-212"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>がに設定されている場合 `None` :</span><span class="sxs-lookup"><span data-stu-id="ffa11-212">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="ffa11-213">`Cache-Control` が `no-store,no-cache` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-213">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="ffa11-214">`Pragma` が `no-cache` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-214">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="ffa11-215">がで、がである場合、 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> `false` およびは <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None` `Cache-Control` `Pragma` に設定され `no-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-215">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="ffa11-216"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> は、通常、 `true` エラーページに対してに設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-216"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="ffa11-217">サンプルアプリの Cache2 ページには、応答を格納しないようにクライアントに指示する応答ヘッダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-217">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="ffa11-218">このサンプルアプリでは、次のヘッダーを含む Cache2 ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-218">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="ffa11-219">場所と期間</span><span class="sxs-lookup"><span data-stu-id="ffa11-219">Location and Duration</span></span>

<span data-ttu-id="ffa11-220">キャッシュを有効にするには、を <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 正の値に設定し、 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> (既定値) またはのいずれかにする必要があり `Any` `Client` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-220">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="ffa11-221">フレームワークによって、ヘッダーは location 値に設定され、その `Cache-Control` 後に `max-age` 応答のが続きます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-221">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="ffa11-222"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>との各オプションは、 `Any` `Client` `Cache-Control` それぞれおよびのヘッダー値に変換さ `public` `private` れます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-222"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="ffa11-223">[Nostore と Location. None](#nostore-and-locationnone)セクションに記載されているよう <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> に、をに設定する `None` と、との両方の `Cache-Control` `Pragma` ヘッダーがに設定さ `no-cache` れます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-223">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="ffa11-224">`Location.Any` ( `Cache-Control` に設定 `public` ) *は、クライアントまたは任意の中間プロキシ* が値 ( [応答キャッシュミドルウェア](xref:performance/caching/middleware)を含む) をキャッシュする可能性があることを示します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-224">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="ffa11-225">`Location.Client` ( `Cache-Control` に設定 `private` ) は *、クライアントのみ* が値をキャッシュできることを示します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-225">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="ffa11-226">[応答キャッシュミドルウェア](xref:performance/caching/middleware)を含め、中間キャッシュで値をキャッシュすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-226">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="ffa11-227">キャッシュ制御ヘッダーは、応答をキャッシュするタイミングと方法について、クライアントと仲介プロキシに関するガイダンスを提供するだけです。</span><span class="sxs-lookup"><span data-stu-id="ffa11-227">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="ffa11-228">クライアントとプロキシが [HTTP 1.1 のキャッシュ仕様](https://tools.ietf.org/html/rfc7234)に従うという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-228">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="ffa11-229">[応答キャッシュミドルウェア](xref:performance/caching/middleware) は、常に仕様によって配置されたキャッシュ規則に従います。</span><span class="sxs-lookup"><span data-stu-id="ffa11-229">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="ffa11-230">次の例では、サンプルアプリの Cache3 ページモデルと、を設定し、 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 既定値のままにして生成されたヘッダーを示し <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-230">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="ffa11-231">このサンプルアプリでは、次のヘッダーを含む Cache3 ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-231">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="ffa11-232">キャッシュプロファイル</span><span class="sxs-lookup"><span data-stu-id="ffa11-232">Cache profiles</span></span>

<span data-ttu-id="ffa11-233">多くのコントローラーアクション属性に対して応答キャッシュ設定を複製するのではなく、で MVC/ページを設定するときに、キャッシュプロファイルをオプションとして構成でき Razor `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-233">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ffa11-234">参照キャッシュプロファイルで見つかった値は、によって既定値として使用され、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 属性で指定されたプロパティによって上書きされます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-234">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="ffa11-235">キャッシュプロファイルを設定します。</span><span class="sxs-lookup"><span data-stu-id="ffa11-235">Set up a cache profile.</span></span> <span data-ttu-id="ffa11-236">次の例は、サンプルアプリの30秒のキャッシュプロファイルを示してい `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-236">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response/samples/3.x/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="ffa11-237">サンプルアプリの Cache4 ページモデルは、キャッシュプロファイルを参照し `Default30` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-237">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="ffa11-238">は、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 次のように適用できます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-238">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* <span data-ttu-id="ffa11-239">Razor ページ: ハンドラーメソッドに属性を適用することはできません。</span><span class="sxs-lookup"><span data-stu-id="ffa11-239">Razor Pages: Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="ffa11-240">MVC コントローラー。</span><span class="sxs-lookup"><span data-stu-id="ffa11-240">MVC controllers.</span></span>
* <span data-ttu-id="ffa11-241">MVC アクションメソッド: メソッドレベルの属性は、クラスレベルの属性で指定された設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ffa11-241">MVC action methods: Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="ffa11-242">結果のヘッダーは、キャッシュプロファイルによって Cache4 ページの応答に適用され `Default30` ます。</span><span class="sxs-lookup"><span data-stu-id="ffa11-242">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="ffa11-243">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="ffa11-243">Additional resources</span></span>

* [<span data-ttu-id="ffa11-244">キャッシュへの応答の格納</span><span class="sxs-lookup"><span data-stu-id="ffa11-244">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="ffa11-245">Cache-control</span><span class="sxs-lookup"><span data-stu-id="ffa11-245">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
