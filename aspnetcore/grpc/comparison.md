---
<span data-ttu-id="820df-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-102">'Blazor'</span></span>
- <span data-ttu-id="820df-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-103">'Identity'</span></span>
- <span data-ttu-id="820df-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-105">'Razor'</span></span>
- <span data-ttu-id="820df-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="820df-107">HTTP API を使用した gRPC サービスの比較</span><span class="sxs-lookup"><span data-stu-id="820df-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="820df-108">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="820df-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="820df-109">この記事では、JSON を使用する HTTP API (ASP.NET Core [Web API](xref:web-api/index) を含む) と対比させて [gRPC サービス](https://grpc.io/docs/guides/)について説明します。</span><span class="sxs-lookup"><span data-stu-id="820df-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="820df-110">アプリに API を提供するために使用するテクノロジは重要な選択であり、gRPC は、HTTP API と比較して特有の利点を備えています。</span><span class="sxs-lookup"><span data-stu-id="820df-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="820df-111">この記事では、gRPC の長所と短所について説明し、他のテクノロジよりも gRPC を使用するのが推奨されるシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="820df-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="820df-112">比較の概要</span><span class="sxs-lookup"><span data-stu-id="820df-112">High-level comparison</span></span>

<span data-ttu-id="820df-113">次の表は、gRPC と、JSON を使用する HTTP API の機能を比較した概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="820df-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="820df-114">機能</span><span class="sxs-lookup"><span data-stu-id="820df-114">Feature</span></span>          | <span data-ttu-id="820df-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="820df-115">gRPC</span></span>                                               | <span data-ttu-id="820df-116">JSON を使用する HTTP API</span><span class="sxs-lookup"><span data-stu-id="820df-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="820df-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-118">'Blazor'</span></span>
- <span data-ttu-id="820df-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-119">'Identity'</span></span>
- <span data-ttu-id="820df-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-121">'Razor'</span></span>
- <span data-ttu-id="820df-122">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-124">'Blazor'</span></span>
- <span data-ttu-id="820df-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-125">'Identity'</span></span>
- <span data-ttu-id="820df-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-127">'Razor'</span></span>
- <span data-ttu-id="820df-128">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-130">'Blazor'</span></span>
- <span data-ttu-id="820df-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-131">'Identity'</span></span>
- <span data-ttu-id="820df-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-133">'Razor'</span></span>
- <span data-ttu-id="820df-134">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-136">'Blazor'</span></span>
- <span data-ttu-id="820df-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-137">'Identity'</span></span>
- <span data-ttu-id="820df-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-139">'Razor'</span></span>
- <span data-ttu-id="820df-140">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-142">'Blazor'</span></span>
- <span data-ttu-id="820df-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-143">'Identity'</span></span>
- <span data-ttu-id="820df-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-145">'Razor'</span></span>
- <span data-ttu-id="820df-146">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-148">'Blazor'</span></span>
- <span data-ttu-id="820df-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-149">'Identity'</span></span>
- <span data-ttu-id="820df-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-151">'Razor'</span></span>
- <span data-ttu-id="820df-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-152">'SignalR' uid:</span></span> 

<span data-ttu-id="820df-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-154">'Blazor'</span></span>
- <span data-ttu-id="820df-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-155">'Identity'</span></span>
- <span data-ttu-id="820df-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-157">'Razor'</span></span>
- <span data-ttu-id="820df-158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-160">'Blazor'</span></span>
- <span data-ttu-id="820df-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-161">'Identity'</span></span>
- <span data-ttu-id="820df-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-163">'Razor'</span></span>
- <span data-ttu-id="820df-164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-166">'Blazor'</span></span>
- <span data-ttu-id="820df-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-167">'Identity'</span></span>
- <span data-ttu-id="820df-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-169">'Razor'</span></span>
- <span data-ttu-id="820df-170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-172">'Blazor'</span></span>
- <span data-ttu-id="820df-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-173">'Identity'</span></span>
- <span data-ttu-id="820df-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-175">'Razor'</span></span>
- <span data-ttu-id="820df-176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-178">'Blazor'</span></span>
- <span data-ttu-id="820df-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-179">'Identity'</span></span>
- <span data-ttu-id="820df-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-181">'Razor'</span></span>
- <span data-ttu-id="820df-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-184">'Blazor'</span></span>
- <span data-ttu-id="820df-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-185">'Identity'</span></span>
- <span data-ttu-id="820df-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-187">'Razor'</span></span>
- <span data-ttu-id="820df-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-190">'Blazor'</span></span>
- <span data-ttu-id="820df-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-191">'Identity'</span></span>
- <span data-ttu-id="820df-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-193">'Razor'</span></span>
- <span data-ttu-id="820df-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-196">'Blazor'</span></span>
- <span data-ttu-id="820df-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-197">'Identity'</span></span>
- <span data-ttu-id="820df-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-199">'Razor'</span></span>
- <span data-ttu-id="820df-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-202">'Blazor'</span></span>
- <span data-ttu-id="820df-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-203">'Identity'</span></span>
- <span data-ttu-id="820df-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-205">'Razor'</span></span>
- <span data-ttu-id="820df-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-208">'Blazor'</span></span>
- <span data-ttu-id="820df-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-209">'Identity'</span></span>
- <span data-ttu-id="820df-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-211">'Razor'</span></span>
- <span data-ttu-id="820df-212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-214">'Blazor'</span></span>
- <span data-ttu-id="820df-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-215">'Identity'</span></span>
- <span data-ttu-id="820df-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-217">'Razor'</span></span>
- <span data-ttu-id="820df-218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-220">'Blazor'</span></span>
- <span data-ttu-id="820df-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-221">'Identity'</span></span>
- <span data-ttu-id="820df-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-223">'Razor'</span></span>
- <span data-ttu-id="820df-224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-226">'Blazor'</span></span>
- <span data-ttu-id="820df-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-227">'Identity'</span></span>
- <span data-ttu-id="820df-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-229">'Razor'</span></span>
- <span data-ttu-id="820df-230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-232">'Blazor'</span></span>
- <span data-ttu-id="820df-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-233">'Identity'</span></span>
- <span data-ttu-id="820df-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-235">'Razor'</span></span>
- <span data-ttu-id="820df-236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-238">'Blazor'</span></span>
- <span data-ttu-id="820df-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-239">'Identity'</span></span>
- <span data-ttu-id="820df-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-241">'Razor'</span></span>
- <span data-ttu-id="820df-242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-244">'Blazor'</span></span>
- <span data-ttu-id="820df-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-245">'Identity'</span></span>
- <span data-ttu-id="820df-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-247">'Razor'</span></span>
- <span data-ttu-id="820df-248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-250">'Blazor'</span></span>
- <span data-ttu-id="820df-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-251">'Identity'</span></span>
- <span data-ttu-id="820df-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-253">'Razor'</span></span>
- <span data-ttu-id="820df-254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-256">'Blazor'</span></span>
- <span data-ttu-id="820df-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-257">'Identity'</span></span>
- <span data-ttu-id="820df-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-259">'Razor'</span></span>
- <span data-ttu-id="820df-260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-262">'Blazor'</span></span>
- <span data-ttu-id="820df-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-263">'Identity'</span></span>
- <span data-ttu-id="820df-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-265">'Razor'</span></span>
- <span data-ttu-id="820df-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-268">'Blazor'</span></span>
- <span data-ttu-id="820df-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-269">'Identity'</span></span>
- <span data-ttu-id="820df-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-271">'Razor'</span></span>
- <span data-ttu-id="820df-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-274">'Blazor'</span></span>
- <span data-ttu-id="820df-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-275">'Identity'</span></span>
- <span data-ttu-id="820df-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-277">'Razor'</span></span>
- <span data-ttu-id="820df-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-280">'Blazor'</span></span>
- <span data-ttu-id="820df-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-281">'Identity'</span></span>
- <span data-ttu-id="820df-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-283">'Razor'</span></span>
- <span data-ttu-id="820df-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-286">'Blazor'</span></span>
- <span data-ttu-id="820df-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-287">'Identity'</span></span>
- <span data-ttu-id="820df-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-289">'Razor'</span></span>
- <span data-ttu-id="820df-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-290">'SignalR' uid:</span></span> 

<span data-ttu-id="820df-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-292">'Blazor'</span></span>
- <span data-ttu-id="820df-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-293">'Identity'</span></span>
- <span data-ttu-id="820df-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-295">'Razor'</span></span>
- <span data-ttu-id="820df-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-298">'Blazor'</span></span>
- <span data-ttu-id="820df-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-299">'Identity'</span></span>
- <span data-ttu-id="820df-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-301">'Razor'</span></span>
- <span data-ttu-id="820df-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-304">'Blazor'</span></span>
- <span data-ttu-id="820df-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-305">'Identity'</span></span>
- <span data-ttu-id="820df-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-307">'Razor'</span></span>
- <span data-ttu-id="820df-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-310">'Blazor'</span></span>
- <span data-ttu-id="820df-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-311">'Identity'</span></span>
- <span data-ttu-id="820df-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-313">'Razor'</span></span>
- <span data-ttu-id="820df-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-316">'Blazor'</span></span>
- <span data-ttu-id="820df-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-317">'Identity'</span></span>
- <span data-ttu-id="820df-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-319">'Razor'</span></span>
- <span data-ttu-id="820df-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-322">'Blazor'</span></span>
- <span data-ttu-id="820df-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-323">'Identity'</span></span>
- <span data-ttu-id="820df-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-325">'Razor'</span></span>
- <span data-ttu-id="820df-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-328">'Blazor'</span></span>
- <span data-ttu-id="820df-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-329">'Identity'</span></span>
- <span data-ttu-id="820df-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-331">'Razor'</span></span>
- <span data-ttu-id="820df-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-334">'Blazor'</span></span>
- <span data-ttu-id="820df-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-335">'Identity'</span></span>
- <span data-ttu-id="820df-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-337">'Razor'</span></span>
- <span data-ttu-id="820df-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-340">'Blazor'</span></span>
- <span data-ttu-id="820df-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-341">'Identity'</span></span>
- <span data-ttu-id="820df-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-343">'Razor'</span></span>
- <span data-ttu-id="820df-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-346">'Blazor'</span></span>
- <span data-ttu-id="820df-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-347">'Identity'</span></span>
- <span data-ttu-id="820df-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-349">'Razor'</span></span>
- <span data-ttu-id="820df-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-352">'Blazor'</span></span>
- <span data-ttu-id="820df-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-353">'Identity'</span></span>
- <span data-ttu-id="820df-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-355">'Razor'</span></span>
- <span data-ttu-id="820df-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="820df-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="820df-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="820df-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="820df-358">'Blazor'</span></span>
- <span data-ttu-id="820df-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="820df-359">'Identity'</span></span>
- <span data-ttu-id="820df-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="820df-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="820df-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="820df-361">'Razor'</span></span>
- <span data-ttu-id="820df-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="820df-362">'SignalR' uid:</span></span> 

<span data-ttu-id="820df-363">--------------- | | コントラクト         | 必須 ( *.proto*)                                | 省略可能 (OpenAPI)            | | プロトコル         | HTTP/2                                             | HTTP                          | | ペイロード          | [Protobuf (小、バイナリ)](#performance)           | JSON (大、人間が判別可能)  | | 規範性 | [厳密な仕様](#strict-specification)      | ゆるい。</span><span class="sxs-lookup"><span data-stu-id="820df-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="820df-364">どのような HTTP でも有効です。</span><span class="sxs-lookup"><span data-stu-id="820df-364">Any HTTP is valid.</span></span>     <span data-ttu-id="820df-365">| | ストリーミング        | [クライアント、サーバー、双方向](#streaming)       | クライアント、サーバー                | | ブラウザーのサポート  | [いいえ (grpc-web が必要)](#limited-browser-support) | はい                           | | セキュリティ         | トランスポート (TLS)                                    | トランスポート (TLS)               | | クライアントコード生成 | [はい](#code-generation)                      | OpenAPI + サードパーティー ツール |</span><span class="sxs-lookup"><span data-stu-id="820df-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="820df-366">gRPC の長所</span><span class="sxs-lookup"><span data-stu-id="820df-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="820df-367">パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="820df-367">Performance</span></span>

<span data-ttu-id="820df-368">gRPC メッセージは、効率的なバイナリ メッセージ形式である [Protobuf](https://developers.google.com/protocol-buffers/docs/overview) を使用してシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="820df-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="820df-369">Protobuf はサーバーおよびクライアント上で、非常に高速にシリアル化を行います。</span><span class="sxs-lookup"><span data-stu-id="820df-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="820df-370">Protobuf のシリアル化では、メッセージ ペイロードが小さくなります。これはモバイル アプリのように帯域幅が限られるシナリオでは重要です。</span><span class="sxs-lookup"><span data-stu-id="820df-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="820df-371">gRPC は、HTTP のメジャー リビジョンである HTTP/2 向けに設計されており、パフォーマンスは HTTP 1.x よりも大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="820df-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="820df-372">バイナリ フレームと圧縮。</span><span class="sxs-lookup"><span data-stu-id="820df-372">Binary framing and compression.</span></span> <span data-ttu-id="820df-373">HTTP/2 プロトコルは、送信と受信の両方においてコンパクトかつ効率的です。</span><span class="sxs-lookup"><span data-stu-id="820df-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="820df-374">単一 TCP 接続での複数の HTTP/2 呼び出しの多重化。</span><span class="sxs-lookup"><span data-stu-id="820df-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="820df-375">多重化により、[ヘッドオブライン ブロッキング](https://en.wikipedia.org/wiki/Head-of-line_blocking)が解消されます。</span><span class="sxs-lookup"><span data-stu-id="820df-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="820df-376">HTTP/2 は gRPC に限定されません。</span><span class="sxs-lookup"><span data-stu-id="820df-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="820df-377">JSON を使用する HTTP API など、多くの要求の種類で、HTTP/2 を使用すると、パフォーマンスの向上による恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="820df-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="820df-378">コード生成</span><span class="sxs-lookup"><span data-stu-id="820df-378">Code generation</span></span>

<span data-ttu-id="820df-379">すべての gRPC フレームワークで、コード生成は最高レベルでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="820df-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="820df-380">gRPC 開発の中核となるファイルは [.proto ファイル](https://developers.google.com/protocol-buffers/docs/proto3)であり、そこで gRPC のサービスとメッセージのコントラクトが定義されます。</span><span class="sxs-lookup"><span data-stu-id="820df-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="820df-381">このファイルから、gRPC フレームワークによって、サービス基本クラス、メッセージ、および完全なクライアントがコード生成されます。</span><span class="sxs-lookup"><span data-stu-id="820df-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="820df-382">サーバーとクライアントの間で *.proto* ファイルを共有すれば、端から端までメッセージとクライアント コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="820df-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="820df-383">クライアントのコード生成によって、クライアントとサーバー上でメッセージが重複しなくなり、厳密に型指定されたクライアントが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="820df-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="820df-384">クライアントを記述する必要がないので、多くのサービスを持つアプリケーションの開発時間が大幅に節約されます。</span><span class="sxs-lookup"><span data-stu-id="820df-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="820df-385">厳密な仕様</span><span class="sxs-lookup"><span data-stu-id="820df-385">Strict specification</span></span>

<span data-ttu-id="820df-386">JSON を使用する HTTP API の公式仕様は存在しません。</span><span class="sxs-lookup"><span data-stu-id="820df-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="820df-387">URL、HTTP 動詞、および応答コードの最適な形式については、開発者が議論します。</span><span class="sxs-lookup"><span data-stu-id="820df-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="820df-388">[gRPC 仕様](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)では、gRPC サービスが従う必要がある形式の規範が示されています。</span><span class="sxs-lookup"><span data-stu-id="820df-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="820df-389">gRPC はプラットフォームや実装の全体にわたって一貫性があるため、議論は不要であり、開発者の時間が節約されます。</span><span class="sxs-lookup"><span data-stu-id="820df-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="820df-390">ストリーム</span><span class="sxs-lookup"><span data-stu-id="820df-390">Streaming</span></span>

<span data-ttu-id="820df-391">HTTP/2 では、有効期間が長いリアルタイム通信ストリームの基盤が提供されます。</span><span class="sxs-lookup"><span data-stu-id="820df-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="820df-392">gRPC では、HTTP/2 を介したストリーミングが最高レベルでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="820df-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="820df-393">gRPC サービスでは、ストリーミングのすべての組み合わせがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="820df-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="820df-394">Unary (ストリーミングなし)</span><span class="sxs-lookup"><span data-stu-id="820df-394">Unary (no streaming)</span></span>
* <span data-ttu-id="820df-395">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="820df-395">Server to client streaming</span></span>
* <span data-ttu-id="820df-396">クライアントからサーバーへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="820df-396">Client to server streaming</span></span>
* <span data-ttu-id="820df-397">双方向ストリーミング</span><span class="sxs-lookup"><span data-stu-id="820df-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="820df-398">期限/タイムアウトとキャンセル</span><span class="sxs-lookup"><span data-stu-id="820df-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="820df-399">gRPC を使用すると、クライアントが RPC の完了を待機する時間を指定できます。</span><span class="sxs-lookup"><span data-stu-id="820df-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="820df-400">[期限](https://grpc.io/blog/deadlines)がサーバーに送信され、期限を越えた場合にどのようなアクションを実行するかをサーバーで決定できます。</span><span class="sxs-lookup"><span data-stu-id="820df-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="820df-401">たとえば、タイムアウト時に、進行中の gRPC/HTTP/データベース要求をサーバーでキャンセルする場合があります。</span><span class="sxs-lookup"><span data-stu-id="820df-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="820df-402">子の gRPC 呼び出しを通して期限やキャンセルを伝達すると、リソース使用量の制限を適用する助けとなります。</span><span class="sxs-lookup"><span data-stu-id="820df-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="820df-403">gRPC での推奨されるシナリオ</span><span class="sxs-lookup"><span data-stu-id="820df-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="820df-404">gRPC は以下のシナリオに適しています。</span><span class="sxs-lookup"><span data-stu-id="820df-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="820df-405">**マイクロサービス**: gRPC は、待機時間が短く、スループットの高い通信のために設計されています。</span><span class="sxs-lookup"><span data-stu-id="820df-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="820df-406">gRPC は、効率が非常に重要となる軽量のマイクロサービスに適しています。</span><span class="sxs-lookup"><span data-stu-id="820df-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="820df-407">**ポイント ツー ポイントのリアルタイム通信**: gRPC での双方向ストリーミングのサポートは非常に優秀です。</span><span class="sxs-lookup"><span data-stu-id="820df-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="820df-408">gRPC サービスでは、ポーリングせずにメッセージをリアルタイムにプッシュできます。</span><span class="sxs-lookup"><span data-stu-id="820df-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="820df-409">**多言語環境**: gRPC ツールでは、一般的な開発言語がすべてサポートされているため、gRPC は多言語環境に適した選択肢となっています。</span><span class="sxs-lookup"><span data-stu-id="820df-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="820df-410">**ネットワーク面の制約がある環境**: gRPC メッセージは、軽量なメッセージ形式である Protobuf でシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="820df-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="820df-411">gRPC メッセージは常に、同等の JSON メッセージよりも小さくなります。</span><span class="sxs-lookup"><span data-stu-id="820df-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="820df-412">gRPC の短所</span><span class="sxs-lookup"><span data-stu-id="820df-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="820df-413">ブラウザーのサポートが限定的</span><span class="sxs-lookup"><span data-stu-id="820df-413">Limited browser support</span></span>

<span data-ttu-id="820df-414">現在のところ、ブラウザーから gRPC サービスを直接呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="820df-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="820df-415">gRPC は HTTP/2 の機能を多用しており、gRPC クライアントをサポートするための Web 要求を通して必要とされるレベルの制御を提供するブラウザーがありません。</span><span class="sxs-lookup"><span data-stu-id="820df-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="820df-416">たとえば、ブラウザーでは呼び出し元に、HTTP/2 を使用するよう求めたり、基になる HTTP/2 フレームへのアクセスを提供したりすることを許可していません。</span><span class="sxs-lookup"><span data-stu-id="820df-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="820df-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) は、ブラウザーで限定的な gRPC サポートを実現する、gRPC チームが提供する追加のテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="820df-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="820df-418">gRPC-Web は、最新のブラウザーをすべてサポートする JavaScript クライアントと、サーバー上の gRPC-Web プロキシの2つの部分で構成されています。</span><span class="sxs-lookup"><span data-stu-id="820df-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="820df-419">gRPC-Web クライアントはプロキシを呼び出し、プロキシは gRPC 要求を gRPC サーバーに転送します。</span><span class="sxs-lookup"><span data-stu-id="820df-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="820df-420">gRPC のすべての機能が gRPC-Web でサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="820df-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="820df-421">クライアントと双方向ストリーミングはサポートされておらず、サーバー ストリーミングのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="820df-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="820df-422">.NET Core では、gRPC-Web が実験的にサポートされています。</span><span class="sxs-lookup"><span data-stu-id="820df-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="820df-423">詳細については、<xref:grpc/browser> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="820df-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="820df-424">人が判読できない</span><span class="sxs-lookup"><span data-stu-id="820df-424">Not human readable</span></span>

<span data-ttu-id="820df-425">HTTP API 要求はテキストとして送信され、人が読み取りと作成を行えます。</span><span class="sxs-lookup"><span data-stu-id="820df-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="820df-426">gRPC メッセージは、既定では Protobuf でエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="820df-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="820df-427">Protobuf での送受信は効率的ですが、そのバイナリ形式は人が判読できません。</span><span class="sxs-lookup"><span data-stu-id="820df-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="820df-428">Protobuf では、正しく逆シリアル化するために、 *.proto* ファイルで指定されているメッセージのインターフェイスに関する説明を必要とします。</span><span class="sxs-lookup"><span data-stu-id="820df-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="820df-429">ネットワーク上の Protobuf ペイロードを分析し、手動で要求を作成するには、追加のツールが必要です。</span><span class="sxs-lookup"><span data-stu-id="820df-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="820df-430">バイナリの Protobuf メッセージをサポートするため、[サーバー リフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)や [gRPC コマンド ライン ツール](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)などの機能が存在します。</span><span class="sxs-lookup"><span data-stu-id="820df-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="820df-431">また、Protobuf メッセージでは、[JSON との間の変換](https://developers.google.com/protocol-buffers/docs/proto3#json)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="820df-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="820df-432">組み込みの JSON 変換によって、デバッグ時に Protobuf メッセージを人が判読できる形式に変換する効率的な方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="820df-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="820df-433">他のフレームワークのシナリオ</span><span class="sxs-lookup"><span data-stu-id="820df-433">Alternative framework scenarios</span></span>

<span data-ttu-id="820df-434">以下のシナリオでは、gRPC よりも他のフレームワークが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="820df-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="820df-435">**ブラウザーでアクセスできる API**: gRPC は、ブラウザーで完全にサポートされているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="820df-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="820df-436">gRPC-Web でブラウザーのサポートを提供可能ですが、制限があり、サーバー プロキシが必要になります。</span><span class="sxs-lookup"><span data-stu-id="820df-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="820df-437">**ブロードキャスト リアルタイム通信**: gRPC ではストリーミングによるリアルタイム通信がサポートされますが、登録済みの接続にメッセージをブロードキャストするという概念がありません。</span><span class="sxs-lookup"><span data-stu-id="820df-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="820df-438">たとえば、チャット ルーム内のすべてのクライアントに新しいチャット メッセージを送信する必要があるチャット ルーム シナリオでは、クライアントに新しいチャット メッセージを個々にストリーミングするため、それぞれに gRPC 呼び出しが必要です。</span><span class="sxs-lookup"><span data-stu-id="820df-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="820df-439">[SignalR](xref:signalr/introduction) は、このシナリオの場合に便利なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="820df-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="820df-440"> には永続的な接続という概念があり、メッセージをブロードキャストするためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="820df-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="820df-441">**プロセス間通信**:受信 gRPC 呼び出しを受け入れるため、プロセスで HTTP/2 サーバーをホストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="820df-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="820df-442">Windows の場合、プロセス間通信[パイプ](/dotnet/standard/io/pipe-operations)が高速で軽量な通信方法です。</span><span class="sxs-lookup"><span data-stu-id="820df-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="820df-443">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="820df-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
