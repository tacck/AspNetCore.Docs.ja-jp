---
<span data-ttu-id="467d4-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-102">'Blazor'</span></span>
- <span data-ttu-id="467d4-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-103">'Identity'</span></span>
- <span data-ttu-id="467d4-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-105">'Razor'</span></span>
- <span data-ttu-id="467d4-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="467d4-107">ASP.NET Core での応答のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="467d4-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="467d4-108">By [John Luo](https://github.com/JunTaoLuo)、 [Rick Anderson](https://twitter.com/RickAndMSFT)、および[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="467d4-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="467d4-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="467d4-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="467d4-110">応答キャッシュを使用すると、クライアントまたはプロキシが web サーバーに対して行う要求の数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="467d4-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="467d4-111">応答キャッシュを使用すると、web サーバーが応答を生成するために実行する作業量も少なくなります。</span><span class="sxs-lookup"><span data-stu-id="467d4-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="467d4-112">応答のキャッシュは、クライアント、プロキシ、およびミドルウェアが応答をキャッシュする方法を指定するヘッダーによって制御されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="467d4-113">[ResponseCache 属性](#responsecache-attribute)は、応答キャッシュヘッダーの設定に関与します。</span><span class="sxs-lookup"><span data-stu-id="467d4-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="467d4-114">クライアントと中間プロキシは、 [HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234)での応答をキャッシュするためのヘッダーを優先する必要があります。</span><span class="sxs-lookup"><span data-stu-id="467d4-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="467d4-115">HTTP 1.1 キャッシュ仕様に従ったサーバー側キャッシュの場合は、[応答キャッシュミドルウェア](xref:performance/caching/middleware)を使用します。</span><span class="sxs-lookup"><span data-stu-id="467d4-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="467d4-116">ミドルウェアは、プロパティを使用して、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> サーバー側のキャッシュ動作に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="467d4-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="467d4-117">HTTP ベースの応答のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="467d4-117">HTTP-based response caching</span></span>

<span data-ttu-id="467d4-118">[HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234)では、インターネットキャッシュの動作方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="467d4-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="467d4-119">キャッシュに使用されるプライマリ HTTP ヘッダーは[cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)で、キャッシュ*ディレクティブ*を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="467d4-120">ディレクティブは、要求に応じてキャッシュ動作を制御し、応答としてサーバーからクライアントへの応答を行います。</span><span class="sxs-lookup"><span data-stu-id="467d4-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="467d4-121">要求と応答はプロキシサーバーを経由して移動し、プロキシサーバーも HTTP 1.1 キャッシュ仕様に準拠している必要があります。</span><span class="sxs-lookup"><span data-stu-id="467d4-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="467d4-122">共通の `Cache-Control` ディレクティブを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="467d4-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="467d4-123">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="467d4-123">Directive</span></span>                                                       | <span data-ttu-id="467d4-124">アクション</span><span class="sxs-lookup"><span data-stu-id="467d4-124">Action</span></span> |
| ---
<span data-ttu-id="467d4-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-126">'Blazor'</span></span>
- <span data-ttu-id="467d4-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-127">'Identity'</span></span>
- <span data-ttu-id="467d4-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-129">'Razor'</span></span>
- <span data-ttu-id="467d4-130">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-132">'Blazor'</span></span>
- <span data-ttu-id="467d4-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-133">'Identity'</span></span>
- <span data-ttu-id="467d4-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-135">'Razor'</span></span>
- <span data-ttu-id="467d4-136">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-138">'Blazor'</span></span>
- <span data-ttu-id="467d4-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-139">'Identity'</span></span>
- <span data-ttu-id="467d4-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-141">'Razor'</span></span>
- <span data-ttu-id="467d4-142">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-144">'Blazor'</span></span>
- <span data-ttu-id="467d4-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-145">'Identity'</span></span>
- <span data-ttu-id="467d4-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-147">'Razor'</span></span>
- <span data-ttu-id="467d4-148">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-150">'Blazor'</span></span>
- <span data-ttu-id="467d4-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-151">'Identity'</span></span>
- <span data-ttu-id="467d4-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-153">'Razor'</span></span>
- <span data-ttu-id="467d4-154">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-156">'Blazor'</span></span>
- <span data-ttu-id="467d4-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-157">'Identity'</span></span>
- <span data-ttu-id="467d4-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-159">'Razor'</span></span>
- <span data-ttu-id="467d4-160">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-162">'Blazor'</span></span>
- <span data-ttu-id="467d4-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-163">'Identity'</span></span>
- <span data-ttu-id="467d4-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-165">'Razor'</span></span>
- <span data-ttu-id="467d4-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-168">'Blazor'</span></span>
- <span data-ttu-id="467d4-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-169">'Identity'</span></span>
- <span data-ttu-id="467d4-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-171">'Razor'</span></span>
- <span data-ttu-id="467d4-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-174">'Blazor'</span></span>
- <span data-ttu-id="467d4-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-175">'Identity'</span></span>
- <span data-ttu-id="467d4-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-177">'Razor'</span></span>
- <span data-ttu-id="467d4-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-180">'Blazor'</span></span>
- <span data-ttu-id="467d4-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-181">'Identity'</span></span>
- <span data-ttu-id="467d4-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-183">'Razor'</span></span>
- <span data-ttu-id="467d4-184">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-186">'Blazor'</span></span>
- <span data-ttu-id="467d4-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-187">'Identity'</span></span>
- <span data-ttu-id="467d4-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-189">'Razor'</span></span>
- <span data-ttu-id="467d4-190">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-192">'Blazor'</span></span>
- <span data-ttu-id="467d4-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-193">'Identity'</span></span>
- <span data-ttu-id="467d4-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-195">'Razor'</span></span>
- <span data-ttu-id="467d4-196">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-198">'Blazor'</span></span>
- <span data-ttu-id="467d4-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-199">'Identity'</span></span>
- <span data-ttu-id="467d4-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-201">'Razor'</span></span>
- <span data-ttu-id="467d4-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-204">'Blazor'</span></span>
- <span data-ttu-id="467d4-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-205">'Identity'</span></span>
- <span data-ttu-id="467d4-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-207">'Razor'</span></span>
- <span data-ttu-id="467d4-208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-210">'Blazor'</span></span>
- <span data-ttu-id="467d4-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-211">'Identity'</span></span>
- <span data-ttu-id="467d4-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-213">'Razor'</span></span>
- <span data-ttu-id="467d4-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-216">'Blazor'</span></span>
- <span data-ttu-id="467d4-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-217">'Identity'</span></span>
- <span data-ttu-id="467d4-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-219">'Razor'</span></span>
- <span data-ttu-id="467d4-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-222">'Blazor'</span></span>
- <span data-ttu-id="467d4-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-223">'Identity'</span></span>
- <span data-ttu-id="467d4-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-225">'Razor'</span></span>
- <span data-ttu-id="467d4-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-228">'Blazor'</span></span>
- <span data-ttu-id="467d4-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-229">'Identity'</span></span>
- <span data-ttu-id="467d4-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-231">'Razor'</span></span>
- <span data-ttu-id="467d4-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-234">'Blazor'</span></span>
- <span data-ttu-id="467d4-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-235">'Identity'</span></span>
- <span data-ttu-id="467d4-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-237">'Razor'</span></span>
- <span data-ttu-id="467d4-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-240">'Blazor'</span></span>
- <span data-ttu-id="467d4-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-241">'Identity'</span></span>
- <span data-ttu-id="467d4-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-243">'Razor'</span></span>
- <span data-ttu-id="467d4-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-246">'Blazor'</span></span>
- <span data-ttu-id="467d4-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-247">'Identity'</span></span>
- <span data-ttu-id="467d4-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-249">'Razor'</span></span>
- <span data-ttu-id="467d4-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-252">'Blazor'</span></span>
- <span data-ttu-id="467d4-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-253">'Identity'</span></span>
- <span data-ttu-id="467d4-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-255">'Razor'</span></span>
- <span data-ttu-id="467d4-256">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-258">'Blazor'</span></span>
- <span data-ttu-id="467d4-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-259">'Identity'</span></span>
- <span data-ttu-id="467d4-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-261">'Razor'</span></span>
- <span data-ttu-id="467d4-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-264">'Blazor'</span></span>
- <span data-ttu-id="467d4-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-265">'Identity'</span></span>
- <span data-ttu-id="467d4-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-267">'Razor'</span></span>
- <span data-ttu-id="467d4-268">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-270">'Blazor'</span></span>
- <span data-ttu-id="467d4-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-271">'Identity'</span></span>
- <span data-ttu-id="467d4-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-273">'Razor'</span></span>
- <span data-ttu-id="467d4-274">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-276">'Blazor'</span></span>
- <span data-ttu-id="467d4-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-277">'Identity'</span></span>
- <span data-ttu-id="467d4-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-279">'Razor'</span></span>
- <span data-ttu-id="467d4-280">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-282">'Blazor'</span></span>
- <span data-ttu-id="467d4-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-283">'Identity'</span></span>
- <span data-ttu-id="467d4-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-285">'Razor'</span></span>
- <span data-ttu-id="467d4-286">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-288">'Blazor'</span></span>
- <span data-ttu-id="467d4-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-289">'Identity'</span></span>
- <span data-ttu-id="467d4-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-291">'Razor'</span></span>
- <span data-ttu-id="467d4-292">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-294">'Blazor'</span></span>
- <span data-ttu-id="467d4-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-295">'Identity'</span></span>
- <span data-ttu-id="467d4-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-297">'Razor'</span></span>
- <span data-ttu-id="467d4-298">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-298">'SignalR' uid:</span></span> 

<span data-ttu-id="467d4-299">-------------------------------- |---title: author: description: monikerRange: ms. author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-300">'Blazor'</span></span>
- <span data-ttu-id="467d4-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-301">'Identity'</span></span>
- <span data-ttu-id="467d4-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-303">'Razor'</span></span>
- <span data-ttu-id="467d4-304">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-304">'SignalR' uid:</span></span> 

<span data-ttu-id="467d4-305">--- | |[public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) |キャッシュは応答を格納できます。</span><span class="sxs-lookup"><span data-stu-id="467d4-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="467d4-306">| |[private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) |応答は、共有キャッシュによって格納されていない必要があります。</span><span class="sxs-lookup"><span data-stu-id="467d4-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="467d4-307">プライベートキャッシュは、応答を格納して再利用できます。</span><span class="sxs-lookup"><span data-stu-id="467d4-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="467d4-308">| |[最大有効期間](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)|クライアントは、指定された秒数よりも有効期間が長い応答を受け入れません。</span><span class="sxs-lookup"><span data-stu-id="467d4-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="467d4-309">例: `max-age=60` (60 秒)、 `max-age=2592000` (1 か月) | |[キャッシュなし](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  | **要求時**: キャッシュは、要求を満たすために格納された応答を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="467d4-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="467d4-310">配信元サーバーはクライアントの応答を再生成し、ミドルウェアはキャッシュに格納されている応答を更新します。</span><span class="sxs-lookup"><span data-stu-id="467d4-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="467d4-311">**応答時**: 配信元サーバーで検証を行わずに、後続の要求に応答を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="467d4-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="467d4-312">| |[ストアなし](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  | **要求時**: キャッシュは要求を格納できません。</span><span class="sxs-lookup"><span data-stu-id="467d4-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="467d4-313">**応答**の場合: キャッシュは、応答の一部を格納することはできません。</span><span class="sxs-lookup"><span data-stu-id="467d4-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="467d4-314">キャッシュでロールを果たすその他のキャッシュヘッダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="467d4-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="467d4-315">Header</span><span class="sxs-lookup"><span data-stu-id="467d4-315">Header</span></span>                                                     | <span data-ttu-id="467d4-316">機能</span><span class="sxs-lookup"><span data-stu-id="467d4-316">Function</span></span> |
| ---
<span data-ttu-id="467d4-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-318">'Blazor'</span></span>
- <span data-ttu-id="467d4-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-319">'Identity'</span></span>
- <span data-ttu-id="467d4-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-321">'Razor'</span></span>
- <span data-ttu-id="467d4-322">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-324">'Blazor'</span></span>
- <span data-ttu-id="467d4-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-325">'Identity'</span></span>
- <span data-ttu-id="467d4-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-327">'Razor'</span></span>
- <span data-ttu-id="467d4-328">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-330">'Blazor'</span></span>
- <span data-ttu-id="467d4-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-331">'Identity'</span></span>
- <span data-ttu-id="467d4-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-333">'Razor'</span></span>
- <span data-ttu-id="467d4-334">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-336">'Blazor'</span></span>
- <span data-ttu-id="467d4-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-337">'Identity'</span></span>
- <span data-ttu-id="467d4-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-339">'Razor'</span></span>
- <span data-ttu-id="467d4-340">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-342">'Blazor'</span></span>
- <span data-ttu-id="467d4-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-343">'Identity'</span></span>
- <span data-ttu-id="467d4-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-345">'Razor'</span></span>
- <span data-ttu-id="467d4-346">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-348">'Blazor'</span></span>
- <span data-ttu-id="467d4-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-349">'Identity'</span></span>
- <span data-ttu-id="467d4-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-351">'Razor'</span></span>
- <span data-ttu-id="467d4-352">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-354">'Blazor'</span></span>
- <span data-ttu-id="467d4-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-355">'Identity'</span></span>
- <span data-ttu-id="467d4-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-357">'Razor'</span></span>
- <span data-ttu-id="467d4-358">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-360">'Blazor'</span></span>
- <span data-ttu-id="467d4-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-361">'Identity'</span></span>
- <span data-ttu-id="467d4-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-363">'Razor'</span></span>
- <span data-ttu-id="467d4-364">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-366">'Blazor'</span></span>
- <span data-ttu-id="467d4-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-367">'Identity'</span></span>
- <span data-ttu-id="467d4-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-369">'Razor'</span></span>
- <span data-ttu-id="467d4-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-372">'Blazor'</span></span>
- <span data-ttu-id="467d4-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-373">'Identity'</span></span>
- <span data-ttu-id="467d4-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-375">'Razor'</span></span>
- <span data-ttu-id="467d4-376">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-378">'Blazor'</span></span>
- <span data-ttu-id="467d4-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-379">'Identity'</span></span>
- <span data-ttu-id="467d4-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-381">'Razor'</span></span>
- <span data-ttu-id="467d4-382">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-384">'Blazor'</span></span>
- <span data-ttu-id="467d4-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-385">'Identity'</span></span>
- <span data-ttu-id="467d4-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-387">'Razor'</span></span>
- <span data-ttu-id="467d4-388">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-390">'Blazor'</span></span>
- <span data-ttu-id="467d4-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-391">'Identity'</span></span>
- <span data-ttu-id="467d4-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-393">'Razor'</span></span>
- <span data-ttu-id="467d4-394">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-396">'Blazor'</span></span>
- <span data-ttu-id="467d4-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-397">'Identity'</span></span>
- <span data-ttu-id="467d4-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-399">'Razor'</span></span>
- <span data-ttu-id="467d4-400">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-402">'Blazor'</span></span>
- <span data-ttu-id="467d4-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-403">'Identity'</span></span>
- <span data-ttu-id="467d4-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-405">'Razor'</span></span>
- <span data-ttu-id="467d4-406">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-408">'Blazor'</span></span>
- <span data-ttu-id="467d4-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-409">'Identity'</span></span>
- <span data-ttu-id="467d4-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-411">'Razor'</span></span>
- <span data-ttu-id="467d4-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-414">'Blazor'</span></span>
- <span data-ttu-id="467d4-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-415">'Identity'</span></span>
- <span data-ttu-id="467d4-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-417">'Razor'</span></span>
- <span data-ttu-id="467d4-418">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-420">'Blazor'</span></span>
- <span data-ttu-id="467d4-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-421">'Identity'</span></span>
- <span data-ttu-id="467d4-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-423">'Razor'</span></span>
- <span data-ttu-id="467d4-424">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-426">'Blazor'</span></span>
- <span data-ttu-id="467d4-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-427">'Identity'</span></span>
- <span data-ttu-id="467d4-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-429">'Razor'</span></span>
- <span data-ttu-id="467d4-430">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-432">'Blazor'</span></span>
- <span data-ttu-id="467d4-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-433">'Identity'</span></span>
- <span data-ttu-id="467d4-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-435">'Razor'</span></span>
- <span data-ttu-id="467d4-436">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-438">'Blazor'</span></span>
- <span data-ttu-id="467d4-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-439">'Identity'</span></span>
- <span data-ttu-id="467d4-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-441">'Razor'</span></span>
- <span data-ttu-id="467d4-442">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-444">'Blazor'</span></span>
- <span data-ttu-id="467d4-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-445">'Identity'</span></span>
- <span data-ttu-id="467d4-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-447">'Razor'</span></span>
- <span data-ttu-id="467d4-448">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-450">'Blazor'</span></span>
- <span data-ttu-id="467d4-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-451">'Identity'</span></span>
- <span data-ttu-id="467d4-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-453">'Razor'</span></span>
- <span data-ttu-id="467d4-454">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-456">'Blazor'</span></span>
- <span data-ttu-id="467d4-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-457">'Identity'</span></span>
- <span data-ttu-id="467d4-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-459">'Razor'</span></span>
- <span data-ttu-id="467d4-460">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-462">'Blazor'</span></span>
- <span data-ttu-id="467d4-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-463">'Identity'</span></span>
- <span data-ttu-id="467d4-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-465">'Razor'</span></span>
- <span data-ttu-id="467d4-466">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-468">'Blazor'</span></span>
- <span data-ttu-id="467d4-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-469">'Identity'</span></span>
- <span data-ttu-id="467d4-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-471">'Razor'</span></span>
- <span data-ttu-id="467d4-472">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-474">'Blazor'</span></span>
- <span data-ttu-id="467d4-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-475">'Identity'</span></span>
- <span data-ttu-id="467d4-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-477">'Razor'</span></span>
- <span data-ttu-id="467d4-478">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-478">'SignalR' uid:</span></span> 

<span data-ttu-id="467d4-479">----------------------------- |---title: author: description: monikerRange: ms. author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-480">'Blazor'</span></span>
- <span data-ttu-id="467d4-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-481">'Identity'</span></span>
- <span data-ttu-id="467d4-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-483">'Razor'</span></span>
- <span data-ttu-id="467d4-484">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-486">'Blazor'</span></span>
- <span data-ttu-id="467d4-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-487">'Identity'</span></span>
- <span data-ttu-id="467d4-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-489">'Razor'</span></span>
- <span data-ttu-id="467d4-490">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-490">'SignalR' uid:</span></span> 

<span data-ttu-id="467d4-491">---- | |[Age](https://tools.ietf.org/html/rfc7234#section-5.1) |配信元サーバーで応答が生成または正常に検証されてからの、秒単位の推定時間。</span><span class="sxs-lookup"><span data-stu-id="467d4-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="467d4-492">| |[有効期限](https://tools.ietf.org/html/rfc7234#section-5.3)|応答が古くなったと見なされるまでの時間。</span><span class="sxs-lookup"><span data-stu-id="467d4-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="467d4-493">| |[Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) |動作を設定するために HTTP/1.0 キャッシュとの下位互換性を維持するために存在し `no-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="467d4-494">`Cache-Control`ヘッダーが存在する場合、 `Pragma` ヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="467d4-495">| |[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) |キャッシュされた `Vary` 応答の元の要求と新しい要求の両方ですべてのヘッダーフィールドが一致する場合を除き、キャッシュされた応答を送信しないように指定します。</span><span class="sxs-lookup"><span data-stu-id="467d4-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="467d4-496">HTTP ベースのキャッシュは、要求のキャッシュ制御ディレクティブを尊重します。</span><span class="sxs-lookup"><span data-stu-id="467d4-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="467d4-497">[Cache-control ヘッダーの HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)では、 `Cache-Control` クライアントから送信された有効なヘッダーを優先するキャッシュが必要です。</span><span class="sxs-lookup"><span data-stu-id="467d4-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="467d4-498">クライアントは、ヘッダー値を使用して要求を行い、 `no-cache` すべての要求に対して新しい応答を強制的に生成するようにサーバーに要求できます。</span><span class="sxs-lookup"><span data-stu-id="467d4-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="467d4-499">`Cache-Control`HTTP キャッシュの目的を検討する場合は、常にクライアント要求ヘッダーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="467d4-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="467d4-500">公式仕様では、キャッシュは、クライアント、プロキシ、およびサーバーのネットワーク経由で要求を満たすことの待機時間とネットワークオーバーヘッドを削減することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="467d4-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="467d4-501">配信元サーバーの負荷を制御する方法であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="467d4-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="467d4-502">ミドルウェアが公式のキャッシュ仕様に準拠しているため、[応答キャッシュミドルウェア](xref:performance/caching/middleware)を使用する場合、開発者はこのキャッシュ動作を制御することはできません。</span><span class="sxs-lookup"><span data-stu-id="467d4-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="467d4-503">[ミドルウェアの計画](https://github.com/dotnet/AspNetCore/issues/2612)された拡張機能は、キャッシュされ `Cache-Control` た応答を提供することを決定するときに、要求のヘッダーを無視するようにミドルウェアを構成する機会です。</span><span class="sxs-lookup"><span data-stu-id="467d4-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="467d4-504">計画された拡張機能により、サーバーの負荷をより適切に制御できるようになります。</span><span class="sxs-lookup"><span data-stu-id="467d4-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="467d4-505">ASP.NET Core のその他のキャッシュテクノロジ</span><span class="sxs-lookup"><span data-stu-id="467d4-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="467d4-506">メモリ内キャッシュ</span><span class="sxs-lookup"><span data-stu-id="467d4-506">In-memory caching</span></span>

<span data-ttu-id="467d4-507">インメモリキャッシュは、キャッシュされたデータを格納するためにサーバーメモリを使用します。</span><span class="sxs-lookup"><span data-stu-id="467d4-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="467d4-508">この種のキャッシュは、1台のサーバーまたは*固定セッション*を使用している複数のサーバーに適しています。</span><span class="sxs-lookup"><span data-stu-id="467d4-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="467d4-509">固定セッションとは、クライアントからの要求が常に同じサーバーにルーティングされて処理されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="467d4-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="467d4-510">詳細については、「<xref:performance/caching/memory>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="467d4-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="467d4-511">分散キャッシュ</span><span class="sxs-lookup"><span data-stu-id="467d4-511">Distributed Cache</span></span>

<span data-ttu-id="467d4-512">アプリがクラウドまたはサーバーファームでホストされている場合は、分散キャッシュを使用してデータをメモリに格納します。</span><span class="sxs-lookup"><span data-stu-id="467d4-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="467d4-513">キャッシュは、要求を処理するサーバー間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="467d4-514">クライアントのキャッシュデータが使用可能な場合、クライアントは、グループ内の任意のサーバーによって処理される要求を送信できます。</span><span class="sxs-lookup"><span data-stu-id="467d4-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="467d4-515">ASP.NET Core は、SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)、および[ncache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)分散キャッシュと連動します。</span><span class="sxs-lookup"><span data-stu-id="467d4-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="467d4-516">詳細については、「<xref:performance/caching/distributed>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="467d4-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="467d4-517">キャッシュ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="467d4-517">Cache Tag Helper</span></span>

<span data-ttu-id="467d4-518">キャッシュタグヘルパーを使用して、MVC ビューまたはページからコンテンツをキャッシュし Razor ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="467d4-519">キャッシュタグヘルパーは、メモリ内キャッシュを使用してデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="467d4-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="467d4-520">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="467d4-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="467d4-521">分散キャッシュ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="467d4-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="467d4-522">分散 Razor キャッシュタグヘルパーを使用して、分散型クラウドまたは web ファームのシナリオで、MVC ビューまたはページからコンテンツをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="467d4-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="467d4-523">分散キャッシュタグヘルパーは、SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)、または[ncache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)を使用してデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="467d4-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="467d4-524">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="467d4-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="467d4-525">ResponseCache 属性</span><span class="sxs-lookup"><span data-stu-id="467d4-525">ResponseCache attribute</span></span>

<span data-ttu-id="467d4-526">は、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 応答キャッシュに適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="467d4-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="467d4-527">認証されたクライアントの情報が含まれているコンテンツのキャッシュを無効にします。</span><span class="sxs-lookup"><span data-stu-id="467d4-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="467d4-528">キャッシュは、ユーザーの id またはユーザーがサインインしているかどうかによって変更されないコンテンツに対してのみ有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="467d4-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="467d4-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>指定されたクエリキーのリストの値によって、格納されている応答を変化させる。</span><span class="sxs-lookup"><span data-stu-id="467d4-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="467d4-530">1つの値を指定した場合 `*` 、ミドルウェアはすべての要求クエリ文字列パラメーターによって応答を変化させることができます。</span><span class="sxs-lookup"><span data-stu-id="467d4-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="467d4-531">プロパティを設定するには、[応答キャッシュミドルウェア](xref:performance/caching/middleware)を有効にする必要があり <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="467d4-532">それ以外の場合は、ランタイム例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="467d4-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="467d4-533">プロパティに対応する HTTP ヘッダーがありません <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 。</span><span class="sxs-lookup"><span data-stu-id="467d4-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="467d4-534">プロパティは、応答キャッシュミドルウェアによって処理される HTTP 機能です。</span><span class="sxs-lookup"><span data-stu-id="467d4-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="467d4-535">ミドルウェアがキャッシュされた応答を提供するには、クエリ文字列とクエリ文字列の値が以前の要求と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="467d4-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="467d4-536">たとえば、次の表に示すような一連の要求と結果を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="467d4-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="467d4-537">Request</span><span class="sxs-lookup"><span data-stu-id="467d4-537">Request</span></span>                          | <span data-ttu-id="467d4-538">結果</span><span class="sxs-lookup"><span data-stu-id="467d4-538">Result</span></span>                    |
| ---
<span data-ttu-id="467d4-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-540">'Blazor'</span></span>
- <span data-ttu-id="467d4-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-541">'Identity'</span></span>
- <span data-ttu-id="467d4-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-543">'Razor'</span></span>
- <span data-ttu-id="467d4-544">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-546">'Blazor'</span></span>
- <span data-ttu-id="467d4-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-547">'Identity'</span></span>
- <span data-ttu-id="467d4-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-549">'Razor'</span></span>
- <span data-ttu-id="467d4-550">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-552">'Blazor'</span></span>
- <span data-ttu-id="467d4-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-553">'Identity'</span></span>
- <span data-ttu-id="467d4-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-555">'Razor'</span></span>
- <span data-ttu-id="467d4-556">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-558">'Blazor'</span></span>
- <span data-ttu-id="467d4-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-559">'Identity'</span></span>
- <span data-ttu-id="467d4-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-561">'Razor'</span></span>
- <span data-ttu-id="467d4-562">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-564">'Blazor'</span></span>
- <span data-ttu-id="467d4-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-565">'Identity'</span></span>
- <span data-ttu-id="467d4-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-567">'Razor'</span></span>
- <span data-ttu-id="467d4-568">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-570">'Blazor'</span></span>
- <span data-ttu-id="467d4-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-571">'Identity'</span></span>
- <span data-ttu-id="467d4-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-573">'Razor'</span></span>
- <span data-ttu-id="467d4-574">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-576">'Blazor'</span></span>
- <span data-ttu-id="467d4-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-577">'Identity'</span></span>
- <span data-ttu-id="467d4-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-579">'Razor'</span></span>
- <span data-ttu-id="467d4-580">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-582">'Blazor'</span></span>
- <span data-ttu-id="467d4-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-583">'Identity'</span></span>
- <span data-ttu-id="467d4-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-585">'Razor'</span></span>
- <span data-ttu-id="467d4-586">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-588">'Blazor'</span></span>
- <span data-ttu-id="467d4-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-589">'Identity'</span></span>
- <span data-ttu-id="467d4-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-591">'Razor'</span></span>
- <span data-ttu-id="467d4-592">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-594">'Blazor'</span></span>
- <span data-ttu-id="467d4-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-595">'Identity'</span></span>
- <span data-ttu-id="467d4-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-597">'Razor'</span></span>
- <span data-ttu-id="467d4-598">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-600">'Blazor'</span></span>
- <span data-ttu-id="467d4-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-601">'Identity'</span></span>
- <span data-ttu-id="467d4-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-603">'Razor'</span></span>
- <span data-ttu-id="467d4-604">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-606">'Blazor'</span></span>
- <span data-ttu-id="467d4-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-607">'Identity'</span></span>
- <span data-ttu-id="467d4-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-609">'Razor'</span></span>
- <span data-ttu-id="467d4-610">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-612">'Blazor'</span></span>
- <span data-ttu-id="467d4-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-613">'Identity'</span></span>
- <span data-ttu-id="467d4-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-615">'Razor'</span></span>
- <span data-ttu-id="467d4-616">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-618">'Blazor'</span></span>
- <span data-ttu-id="467d4-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-619">'Identity'</span></span>
- <span data-ttu-id="467d4-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-621">'Razor'</span></span>
- <span data-ttu-id="467d4-622">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-622">'SignalR' uid:</span></span> 

<span data-ttu-id="467d4-623">---------------- |---title: author: description: monikerRange: ms. author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-624">'Blazor'</span></span>
- <span data-ttu-id="467d4-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-625">'Identity'</span></span>
- <span data-ttu-id="467d4-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-627">'Razor'</span></span>
- <span data-ttu-id="467d4-628">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-630">'Blazor'</span></span>
- <span data-ttu-id="467d4-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-631">'Identity'</span></span>
- <span data-ttu-id="467d4-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-633">'Razor'</span></span>
- <span data-ttu-id="467d4-634">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-636">'Blazor'</span></span>
- <span data-ttu-id="467d4-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-637">'Identity'</span></span>
- <span data-ttu-id="467d4-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-639">'Razor'</span></span>
- <span data-ttu-id="467d4-640">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-642">'Blazor'</span></span>
- <span data-ttu-id="467d4-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-643">'Identity'</span></span>
- <span data-ttu-id="467d4-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-645">'Razor'</span></span>
- <span data-ttu-id="467d4-646">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-648">'Blazor'</span></span>
- <span data-ttu-id="467d4-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-649">'Identity'</span></span>
- <span data-ttu-id="467d4-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-651">'Razor'</span></span>
- <span data-ttu-id="467d4-652">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-654">'Blazor'</span></span>
- <span data-ttu-id="467d4-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-655">'Identity'</span></span>
- <span data-ttu-id="467d4-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-657">'Razor'</span></span>
- <span data-ttu-id="467d4-658">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-660">'Blazor'</span></span>
- <span data-ttu-id="467d4-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-661">'Identity'</span></span>
- <span data-ttu-id="467d4-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-663">'Razor'</span></span>
- <span data-ttu-id="467d4-664">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-666">'Blazor'</span></span>
- <span data-ttu-id="467d4-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-667">'Identity'</span></span>
- <span data-ttu-id="467d4-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-669">'Razor'</span></span>
- <span data-ttu-id="467d4-670">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-672">'Blazor'</span></span>
- <span data-ttu-id="467d4-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-673">'Identity'</span></span>
- <span data-ttu-id="467d4-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-675">'Razor'</span></span>
- <span data-ttu-id="467d4-676">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="467d4-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="467d4-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="467d4-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="467d4-678">'Blazor'</span></span>
- <span data-ttu-id="467d4-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="467d4-679">'Identity'</span></span>
- <span data-ttu-id="467d4-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="467d4-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="467d4-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="467d4-681">'Razor'</span></span>
- <span data-ttu-id="467d4-682">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="467d4-682">'SignalR' uid:</span></span> 

<span data-ttu-id="467d4-683">------------- | |`http://example.com?key1=value1` |サーバーから返されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="467d4-684">| |`http://example.com?key1=value1` |ミドルウェアから返されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="467d4-685">| |`http://example.com?key1=value2` |サーバーから返されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="467d4-686">最初の要求はサーバーによって返され、ミドルウェアにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="467d4-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="467d4-687">2番目の要求は、クエリ文字列が前の要求と一致するため、ミドルウェアによって返されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="467d4-688">クエリ文字列値が以前の要求と一致しないので、3番目の要求がミドルウェアキャッシュにありません。</span><span class="sxs-lookup"><span data-stu-id="467d4-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="467d4-689">は、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> を使用してを構成および作成するために使用され <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="467d4-690">は、 `ResponseCacheFilter` 適切な HTTP ヘッダーと応答の機能を更新する作業を実行します。</span><span class="sxs-lookup"><span data-stu-id="467d4-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="467d4-691">フィルター:</span><span class="sxs-lookup"><span data-stu-id="467d4-691">The filter:</span></span>

* <span data-ttu-id="467d4-692">、、およびの既存のヘッダーを削除 `Vary` `Cache-Control` `Pragma` します。</span><span class="sxs-lookup"><span data-stu-id="467d4-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="467d4-693">で設定されたプロパティに基づいて、適切なヘッダーを書き込み <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="467d4-694">が設定されている場合は、応答キャッシュ HTTP 機能を更新し <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="467d4-695">要因</span><span class="sxs-lookup"><span data-stu-id="467d4-695">Vary</span></span>

<span data-ttu-id="467d4-696">このヘッダーは、プロパティが設定されている場合にのみ書き込まれ <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="467d4-697">プロパティの値に設定されたプロパティ `Vary` 。</span><span class="sxs-lookup"><span data-stu-id="467d4-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="467d4-698">次の例では、プロパティを使用し <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="467d4-699">サンプルアプリを使用して、ブラウザーのネットワークツールで応答ヘッダーを表示します。</span><span class="sxs-lookup"><span data-stu-id="467d4-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="467d4-700">次の応答ヘッダーは、Cache1 ページの応答と共に送信されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="467d4-701">NoStore と Location。なし</span><span class="sxs-lookup"><span data-stu-id="467d4-701">NoStore and Location.None</span></span>

<span data-ttu-id="467d4-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>その他のほとんどのプロパティをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="467d4-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="467d4-703">このプロパティがに設定されている場合 `true` 、 `Cache-Control` ヘッダーはに設定され `no-store` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="467d4-704"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>がに設定されている場合 `None` :</span><span class="sxs-lookup"><span data-stu-id="467d4-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="467d4-705">`Cache-Control` は `no-store,no-cache` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="467d4-706">`Pragma` は `no-cache` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="467d4-707">がで、がである場合、 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> `false` およびは <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None` `Cache-Control` `Pragma` に設定され `no-cache` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="467d4-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>は、通常、 `true` エラーページに対してに設定されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="467d4-709">サンプルアプリの Cache2 ページには、応答を格納しないようにクライアントに指示する応答ヘッダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="467d4-710">このサンプルアプリでは、次のヘッダーを含む Cache2 ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="467d4-711">場所と期間</span><span class="sxs-lookup"><span data-stu-id="467d4-711">Location and Duration</span></span>

<span data-ttu-id="467d4-712">キャッシュを有効にするには、を <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 正の値に設定し、 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> (既定値) またはのいずれかにする必要があり `Any` `Client` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="467d4-713">フレームワークによって、ヘッダーは location 値に設定され、その `Cache-Control` 後に `max-age` 応答のが続きます。</span><span class="sxs-lookup"><span data-stu-id="467d4-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="467d4-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>との各オプションは、 `Any` `Client` `Cache-Control` それぞれおよびのヘッダー値に変換さ `public` `private` れます。</span><span class="sxs-lookup"><span data-stu-id="467d4-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="467d4-715">[Nostore と Location. None](#nostore-and-locationnone)セクションに記載されているよう <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> に、をに設定する `None` と、との両方の `Cache-Control` `Pragma` ヘッダーがに設定さ `no-cache` れます。</span><span class="sxs-lookup"><span data-stu-id="467d4-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="467d4-716">`Location.Any`( `Cache-Control` に設定 `public` )*は、クライアントまたは任意の中間プロキシ*が値 ([応答キャッシュミドルウェア](xref:performance/caching/middleware)を含む) をキャッシュする可能性があることを示します。</span><span class="sxs-lookup"><span data-stu-id="467d4-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="467d4-717">`Location.Client`( `Cache-Control` に設定 `private` ) は *、クライアントのみ*が値をキャッシュできることを示します。</span><span class="sxs-lookup"><span data-stu-id="467d4-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="467d4-718">[応答キャッシュミドルウェア](xref:performance/caching/middleware)を含め、中間キャッシュで値をキャッシュすることはできません。</span><span class="sxs-lookup"><span data-stu-id="467d4-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="467d4-719">キャッシュ制御ヘッダーは、応答をキャッシュするタイミングと方法について、クライアントと仲介プロキシに関するガイダンスを提供するだけです。</span><span class="sxs-lookup"><span data-stu-id="467d4-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="467d4-720">クライアントとプロキシが[HTTP 1.1 のキャッシュ仕様](https://tools.ietf.org/html/rfc7234)に従うという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="467d4-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="467d4-721">[応答キャッシュミドルウェア](xref:performance/caching/middleware)は、常に仕様によって配置されたキャッシュ規則に従います。</span><span class="sxs-lookup"><span data-stu-id="467d4-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="467d4-722">次の例では、サンプルアプリの Cache3 ページモデルと、を設定し、 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 既定値のままにして生成されたヘッダーを示し <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="467d4-723">このサンプルアプリでは、次のヘッダーを含む Cache3 ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="467d4-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="467d4-724">キャッシュプロファイル</span><span class="sxs-lookup"><span data-stu-id="467d4-724">Cache profiles</span></span>

<span data-ttu-id="467d4-725">多くのコントローラーアクション属性に対して応答キャッシュ設定を複製するのではなく、で MVC/ページを設定するときに、キャッシュプロファイルをオプションとして構成でき Razor `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="467d4-726">参照キャッシュプロファイルで見つかった値は、によって既定値として使用され、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 属性で指定されたプロパティによって上書きされます。</span><span class="sxs-lookup"><span data-stu-id="467d4-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="467d4-727">キャッシュプロファイルを設定します。</span><span class="sxs-lookup"><span data-stu-id="467d4-727">Set up a cache profile.</span></span> <span data-ttu-id="467d4-728">次の例は、サンプルアプリの30秒のキャッシュプロファイルを示してい `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="467d4-729">サンプルアプリの Cache4 ページモデルは、キャッシュプロファイルを参照し `Default30` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="467d4-730">は、 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 次のように適用できます。</span><span class="sxs-lookup"><span data-stu-id="467d4-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="467d4-731">ページハンドラー (クラス): ハンドラーメソッドに属性を適用することはできません。</span><span class="sxs-lookup"><span data-stu-id="467d4-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="467d4-732">MVC コントローラー (クラス)。</span><span class="sxs-lookup"><span data-stu-id="467d4-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="467d4-733">MVC アクション (メソッド): メソッドレベルの属性は、クラスレベルの属性で指定された設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="467d4-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="467d4-734">結果のヘッダーは、キャッシュプロファイルによって Cache4 ページの応答に適用され `Default30` ます。</span><span class="sxs-lookup"><span data-stu-id="467d4-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="467d4-735">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="467d4-735">Additional resources</span></span>

* [<span data-ttu-id="467d4-736">キャッシュへの応答の格納</span><span class="sxs-lookup"><span data-stu-id="467d4-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="467d4-737">Cache-control</span><span class="sxs-lookup"><span data-stu-id="467d4-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
